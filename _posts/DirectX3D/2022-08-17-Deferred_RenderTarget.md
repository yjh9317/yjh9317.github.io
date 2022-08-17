---
title: Deferred RenderTarget
date: 2022-08-17
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---


Deferred
==================
* MRT의 한종류로 지연 렌더링이라고도 한다.

* Deferred는 Depth Stencil를 가지고 있지 않고 SwapChain의 DepthStencil를 가져와서 사용한다. 

* Deferred에서는 각 텍스쳐의 rgba를 저장하고 Merge과정에서 그 텍스쳐들의 rgba를 이용한다.

* 종류
    * 텍스쳐 기존색상을 가지고 있는 ColorRenderTexture
    * 텍스쳐의 픽셀이 자기의 노말벡터를 RGB에 저장하고 있는 NormalRenderTexture
    * 텍스쳐의 픽셀의 자기의 좌표정보를 RGB에 저장하고 있는 PositionRenderTexture
    * 그 외 나머지를 저장할 DataRenderTexture

<br>

MRT 생성
======================

 * Deferred Texture
   * D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE : 렌더타겟 및 리소스로 사용 
   * ColorTargetTex는 색상이므로 0~1까지 표현 DXGI_FORMAT_R8G8B8A8_UNORM
   * 그 외 Texture들은 -1 ~ 1까지 부동소수점으로 자세히 표현하기 위해         DXGI_FORMAT_R32G32B32A32_FLOAT

<br>


        void CRenderMgr::CreateMRT()
        {
            // =============
            // SwapChain MRT
            // =============
            m_arrMRT[(UINT)MRT_TYPE::SWAPCHAIN] = new CMRT;

            Ptr<CTexture> pRTTex = CResMgr::GetInst()->FindRes<CTexture>(L"RenderTargetTex");
            Ptr<CTexture> pDSTex = CResMgr::GetInst()->FindRes<CTexture>(L"DepthStencilTex");

            m_arrMRT[(UINT)MRT_TYPE::SWAPCHAIN]->Create(1, &pRTTex, pDSTex);



            Vec2 vResolution = CDevice::GetInst()->GetRenderResolution();


            // ============
            // Deferred MRT
            // ============

            {
                Ptr<CTexture> arrTex[8] =
                {
                    CResMgr::GetInst()->CreateTexture( L"ColorTargetTex" , (UINT)vResolution.x, (UINT)vResolution.y
                        , DXGI_FORMAT_R8G8B8A8_UNORM, D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE, true),
                    
                    CResMgr::GetInst()->CreateTexture(L"NormalTargetTex", (UINT)vResolution.x, (UINT)vResolution.y
                        , DXGI_FORMAT_R32G32B32A32_FLOAT, D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE, true),

                    CResMgr::GetInst()->CreateTexture(L"PositionTargetTex", (UINT)vResolution.x, (UINT)vResolution.y
                        , DXGI_FORMAT_R32G32B32A32_FLOAT, D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE, true),
                    
                    CResMgr::GetInst()->CreateTexture(L"DataTargetTex", (UINT)vResolution.x, (UINT)vResolution.y
                        , DXGI_FORMAT_R32G32B32A32_FLOAT, D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE, true)
                };

                m_arrMRT[(UINT)MRT_TYPE::DEFERRED] = new CMRT;
                m_arrMRT[(UINT)MRT_TYPE::DEFERRED]->Create(4, arrTex, nullptr);
            }

        }

<br><br>

Shader
==============
 * Deferred Shader에는 각각의 텍스쳐 색상값을 저장함
 * Merge Shader에서는 Deferred Shader에서 저장한 텍스쳐 색상을 이용하여 샘플링함.

<br>

        #ifndef _STD3D_DEFERRED
        #define _STD3D_DEFERRED

        #include "value.fx"

        struct VTX_IN
        {
            float3 vPos : POSITION;
            float2 vUV : TEXCOORD;
            
            float3 vTangent : TANGENT;
            float3 vNormal : NORMAL;
            float3 vBinormal : BINORMAL;
        };

        struct VTX_OUT
        {
            float4 vPosition : SV_Position;
            float2 vUV : TEXCOORD;
            
            float3 vViewPos : POSITION;
            
            float3 vViewTangent : TANGENT;
            float3 vViewNormal : NORMAL;
            float3 vViewBinormal : BINORMAL;
        };

        // =========================
        // Std3D_Deferred
        // g_tex_0 : Output Texture
        // g_tex_1 : Normal Map
        // DOMAIN : Deferred
        // Rasterizer : CULL_BACK
        // DepthStencilState : LESS
        // BlendState : DEFAULT
        // =========================
        VTX_OUT VS_Std3D_Deferred(VTX_IN _in)
        {
            VTX_OUT output = (VTX_OUT) 0.f;
                
            output.vPosition = mul(float4(_in.vPos, 1.f), g_matWVP);
            
            return output;
        }

        // PS_OUT이라는 구조체 안에 각각 텍스쳐 색상을 저장.
        // 이대로 병합(Merge)하지 않고 사용한다면 SwapChain의 Depth Stencil에 깊이만 남기고
        // 색상은 사용되지 않아 깊이통과가 된다면 검은색의 메쉬모양이 뜬다.

        struct PS_OUT
        {
            float4 vColor       : SV_Target0;   // 컬러 텍스쳐
            float4 vNormal      : SV_Target1;   // 노말 텍스쳐
            float4 vPosition    : SV_Target2;   // 포지션 텍스쳐
            float4 vData        : SV_Target3;   // 데이터 텍스쳐
        };

        PS_OUT PS_Std3D_Deferred(VTX_OUT _in)
        {
            PS_OUT output = (PS_OUT) 0.f;
            
            output.vColor = float4(1.f, 0.f, 0.f, 1.f);
            output.vNormal = float4(0.f, 1.f, 0.f, 1.f);
            output.vPosition = float4(0.f, 0.f, 1.f, 1.f);
            output.vData = float4(1.f, 0.f, 1.f, 1.f);
            
            return output;
        }







        // ===============
        // Merge Shader
        // Mesh : RectMesh

        // g_int_0 : Target Index

        // g_tex_0 : Color Target
        // g_tex_1 : Norma lTarget
        // g_tex_2 : Position Target
        // g_tex_3 : Data Target
        // ===============
        struct VS_MERGE_IN
        {
            float3 vPos : POSITION;
        };

        struct VS_MERGE_OUT
        {
            float4 vPosition : SV_Position;
        };

        VS_MERGE_OUT VS_Merge(VS_MERGE_IN _in)
        {
            VS_MERGE_OUT output = (VS_MERGE_OUT) 0.f;

            // SV_Position은 픽셀의 위치를 그대로 받아옴
            // 한 정점의 길이가 0.5인 RectMesh를 사용하므로 *2를 해서 -1 ~ 1로 만듦

            output.vPosition = float4(_in.vPos * 2.f, 1.f);
            
            return output;
        }

        float4 PS_Merge(VS_MERGE_OUT _in) : SV_Target0
        {
            float4 vOutColor = (float4) 0.f;
            
            float2 vUV = _in.vPosition.xy / vResolution.xy;    

            if(0 == g_int_0)
            {
                vOutColor = g_tex_0.Sample(g_sam_0, vUV);
            }
            else if(1 == g_int_0)
            {
                vOutColor = g_tex_1.Sample(g_sam_0, vUV);
            }
            else if(2 == g_int_0)
            {
                vOutColor = g_tex_2.Sample(g_sam_0, vUV);
            }
            else
            {
                vOutColor = g_tex_3.Sample(g_sam_0, vUV);
            }        
            
            return vOutColor;
        }
        #endif


<br>

Forward와 Deffered
==============================
  * Forward (단일 렌더타겟)
    * 하나의 RenderTarget으로 한번에 렌더링해서 SwapChain의 RenderTarget으로 설정하는 방식<br><br>
  * Deferred (다중 렌더타겟 , 지연렌더링이라고도 함)
    * 별도의 RenderTarget Texture를 생성해서 각각 렌더링을 하고 메인 RenderTarget <br>(SwapChain의 RenderTarget)에 복사하는 방식
