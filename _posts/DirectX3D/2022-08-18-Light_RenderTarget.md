---
title: Light RenderTarget
date: 2022-08-19
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

Light RenderTarget
==========================
* MRT의 한 종류로 광원 렌더링이다.
* 과정
  * Deferred에서 필요한 데이터들을 저장한 텍스쳐들을 Light Shader에 가져온다.
  * 가져온 Deferred 텍스쳐를 이용하여 각각의 광원을 연산한다.
  * 각각 광원을 연산한 후, 기존의 색상과 곱해서 빛을 누적한 다음 최종색상을 SwapChain의 RenderTarget에 보낸다.

<br>

* Forward 에서는 Lighting을 처리할 때는 물체가 기준이므로 각 물체의 Shader에서 광원 개수만큼 빛을 처리했지만,<br>
  Deferred 에서는 광원이 기준이므로 Deferred의 텍스쳐들을 이용하여 빛의 영역을 따져서 영역 안에 있는 픽셀이 호출되는지를 검사하고 빛을 처리한다.

<br>

* Light RenderTarget 종류
  * Diffuse(자연광)
  * Specular(반사광)
  * ShadowPower(그림자 세기)

<br>

Light RenderTarget 생성
=================================

 * Deferred와 마찬가지로 렌더타겟 및 리소스로 사용(D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE)
 * Diffuse,Specular는 빛의 색상을 저장하기 위해 R8G8B8A8_UNORM 사용
 * ShadowPower는 그림자의 세기를 저장하기 위해 R32_Float를 사용
 
<br><br>

		Ptr<CTexture> arrTex[8] =
		{
			CResMgr::GetInst()->CreateTexture(L"DiffuseTargetTex" , (UINT)vResolution.x, (UINT)vResolution.y
				, DXGI_FORMAT_R8G8B8A8_UNORM, D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE, true),

			CResMgr::GetInst()->CreateTexture(L"SpecularTargetTex", (UINT)vResolution.x, (UINT)vResolution.y
				, DXGI_FORMAT_R8G8B8A8_UNORM, D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE, true),

			CResMgr::GetInst()->CreateTexture(L"ShadowPowerTargetTex", (UINT)vResolution.x, (UINT)vResolution.y
				, DXGI_FORMAT_R32_FLOAT, D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE, true),
		};


<br><br>

Light Shader
==========================
* 빛의 종류에 맞춰 Shader 생성
* Light는 SrcRgb와 DestRgb가 1:1 비율인 Blend를 사용한다.

* LightType종류
  * Directional(태양광)
    * RectMesh로 화면 전체 픽셀을 호출한다.
  * Point (전구,횃불같은 빛)
    * SphereMesh를 이용한다.
  * Spot (손전등) 
    * ConeMesh를 이용한다.

<br><br>


Directional Light
================================
* 3D Light의 종류중 하나로, 태양처럼 모든 곳에 빛을 비추는 타입.

* Directional Light는 화면에 있는 모든 픽셀을 호출 하기 위해 RectMesh를 사용한다.<br><br>


      // ========================
      // Directional Light Shader
      // MRT      : Light
      // mesh     : RectMesh
      // RS       : CULL_BACK
      // DS       : NO_TEST_NO_WRITE
      // BS       : ONE_ONE

      #define LightIdx        g_int_0     // 체크할 광원의 인덱스
      #define PositionTarget  g_tex_0     // 빛의 영역이 받는지 체크하기 위한 위치값(Position)
      #define NormalTarget    g_tex_1     // 빛의 색상 계산을 위한 Normal Vector
      // ========================

      struct VS_DIR_IN
      {
          float3 vPos : POSITION;
      };

      struct VS_DIR_OUT
      {
          float4 vPosition : SV_Position;
      };

      VS_DIR_OUT VS_Directional(VS_DIR_IN _in)
      {
          VS_DIR_OUT output = (VS_DIR_OUT) 0.f;

          output.vPosition = float4(_in.vPos * 2.f, 1.f);
          // 원점으로부터 길이가 0.5인 RectMesh를 사용하기 때문에 *2를 해서 1로 화면 전체로 비춰준다.
          
          return output;
      }

      struct PS_DIR_OUT
      {
          float4 vDiffuse : SV_Target0;   // 자연광
          float4 vSpecular : SV_Target1;  // 반사광
          float4 vShadowPow : SV_Target2; // 그림자의 세기
      };

      PS_DIR_OUT PS_Directional(VS_DIR_OUT _in)
      {
          PS_DIR_OUT output = (PS_DIR_OUT) 0.f;
          
          float2 vUV = _in.vPosition.xy / vResolution.xy;
          
          float3 vViewPos = PositionTarget.Sample(g_sam_0, vUV).xyz;
          float3 vViewNormal = NormalTarget.Sample(g_sam_0, vUV).xyz;
          
          
          // 물체가 그려지지 않은 영역은 빛을 받을 수 없다.
          if (vViewPos.z == 0.f)
              clip(-1);
              
          tLightColor color = (tLightColor) 0.f;
          CalculateLight3D(vViewPos, vViewNormal, LightIdx, color);  // 3DLighting에 있는 함수
              
          output.vDiffuse     = color.vDiff + color.vAmb;
          output.vSpecular    = color.vSpec;
          output.vShadowPow   = (float4) 0.f;
          
          // ImGUI가 AlphaBlend를 지원하기 때문에 맞춰주기 위해 알파값을 1로 설정
          output.vDiffuse.a = 1.f;
          output.vSpecular.a = 1.f;
          output.vShadowPow.a = 1.f;
          
          
          return output;
      }