---
title: LandScape
date: 2022-09-20
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

LandScape
==================
* 3d 지형을 형성하는 컴포넌트

<br>

* LOD 디자인을 사용
  * LOD(Level Of Detail) : 카메라의 거리에 따라 정점의 개수가 달라지는 방식

<br>

* 테셀레이션을 이용하여 정점을 만들고 높이맵을 이용하여 각 정점에 대한 높이를 적용시킨다.
  * 높이맵을 저장하는 텍스쳐에서 추출해서 정점에 적용시키는 방식

<br>



* 만약 정점이 호출될 때 정점 자신만의 노말을 계산한다면 모든 정점의 노말이 비슷해져서 명암이 구분이 되지 않는다
  * 그렇기에 uv로 주변의 높이를 받아와서 주변의 정점에 적용 시키고 난뒤 탄젠트,바이노말을 구하고 그 둘을 외적해서 현재 정점의 노말값을 구한다
  * 주변의 정점과 비교해서 노말값이 들어가기 때문에 명암이 구분된다.

<br><br>

메쉬 및 쉐이더 생성코드
================



        void CLandScape::CreateMesh()
        {
            // 지형 메쉬 설정
            Ptr<CMesh> pMesh = CResMgr::GetInst()->FindRes<CMesh>(L"LandscapeMesh");
            // ResMgr은 리소스를 관리하는 매니저

            // 메쉬 만들기
            // 기존에 참조하던 메쉬는 삭제
            if (nullptr != pMesh)
            {
                // 삭제
                CResMgr::GetInst()->ForceDeleteRes<CTexture>(L"LandscapeMesh");
                pMesh = nullptr;
            }

            vector<Vtx> vecVtx;
            vector<UINT> vecIdx;

            Vtx v;

            // 정점 배치
            for (UINT row = 0; row < m_iZFaceCount + 1; ++row)
            {
                for (UINT col = 0; col < m_iXFaceCount + 1; ++col)
                {
                    v.vPos = Vec3((float)col, 0.f, (float)row);
                    v.vUV = Vec2(col, m_iZFaceCount - row);

                    v.vNormal = Vec3(0.f, 1.f, 0.f);
                    v.vTangent = Vec3(1.f, 0.f, 0.f);
                    v.vBinormal = Vec3(0.f, 0.f, -1.f);

                    v.vColor = Vec4(1.f, 0.f, 1.f, 1.f);

                    vecVtx.push_back(v);
                }
            }

            // 인덱스
            for (UINT row = 0; row < m_iZFaceCount; ++row)
            {
                for (UINT col = 0; col < m_iXFaceCount; ++col)
                {
                    // 0
                    // | \
                    // 2- 1
                    vecIdx.push_back(row * (m_iXFaceCount + 1) + col + m_iXFaceCount + 1);
                    vecIdx.push_back(row * (m_iXFaceCount + 1) + col + 1);
                    vecIdx.push_back(row * (m_iXFaceCount + 1) + col);

                    // 1- 2
                    //  \ |
                    //    0
                    vecIdx.push_back(row * (m_iXFaceCount + 1) + col + 1);
                    vecIdx.push_back(row * (m_iXFaceCount + 1) + col + m_iXFaceCount + 1);
                    vecIdx.push_back(row * (m_iXFaceCount + 1) + col + m_iXFaceCount + 1 + 1);
                }
            }

             // 0
             // | \
             // 2- 1

             // 1- 2
             //  \ |
             //    0
             /* 위와 같이 회전해도 같은 인덱스가 나오도록 만들어야 쉐이더에서 패치 안의 정점 번호에 대응시키기 때문에 크랙
             (위 패치의 정점과 아래 패치의 정점의 개수가 달라 높이가 달라지는 현상)이 생기지 않음
             */
            pMesh = new CMesh;
            pMesh->Create(vecVtx.data(), (UINT)vecVtx.size(), vecIdx.data(), (UINT)vecIdx.size());
            CResMgr::GetInst()->AddRes<CMesh>(L"LandscapeMesh", pMesh.Get(), true);
            SetMesh(pMesh);
        }


        void CLandScape::CreateShaderMaterial()
        {

            // ======================
            // 전용 쉐이더 및 재질 생성
            // ======================		
            Ptr<CGraphicsShader> pShader = CResMgr::GetInst()->FindRes<CGraphicsShader>(L"LandScapeShader");

            if (nullptr == pShader)
            {
                pShader = new CGraphicsShader;
                pShader->CreateVertexShader(L"shader\\LandScape.fx", "VS_LandScape");
                pShader->CreateHullShader(L"shader\\LandScape.fx", "HS_LandScape");
                pShader->CreateDomainShader(L"shader\\LandScape.fx", "DS_LandScape");
                pShader->CreatePixelShader(L"shader\\LandScape.fx", "PS_LandScape");
                pShader->SetTopology(D3D11_PRIMITIVE_TOPOLOGY::D3D11_PRIMITIVE_TOPOLOGY_3_CONTROL_POINT_PATCHLIST);

                pShader->SetShaderDomain(SHADER_DOMAIN::DOMAIN_DEFERRED);
                pShader->SetRSType(RS_TYPE::CULL_BACK);
                //pShader->SetRSType(RS_TYPE::WIRE_FRAME);
                pShader->SetBSType(BS_TYPE::DEFAULT);
                pShader->SetDSType(DS_TYPE::LESS);

                CResMgr::GetInst()->AddRes<CGraphicsShader>(L"LandScapeShader", pShader.Get(), true);
            }

            Ptr<CMaterial> pMtrl = CResMgr::GetInst()->FindRes<CMaterial>(L"material\\LandScapeMtrl.mtrl");

            if (nullptr == pMtrl)
            {
                pMtrl = new CMaterial;
                pMtrl->SetShader(pShader);
                CResMgr::GetInst()->AddRes<CMaterial>(L"material\\LandScapeMtrl.mtrl", pMtrl.Get(), true);
            }

            SetSharedMaterial(pMtrl);
            // 재질 설정
        }



<br><br><br>

쉐이더 코드
====================

        #ifndef _LANDSCAPE
        #define _LANDSCAPE

        #include "value.fx"
        #include "func.fx"

        // ================
        // Landscape Shader
        #define FaceXCount              g_int_0
        #define FaceZCount              g_int_1  

        #define HeightMapResolution     g_vec2_0

        #define HeightMap               g_tex_0
        // ================
        struct VTX_IN
        {
            float3 vPos : POSITION;    
            float2 vUV : TEXCOORD;    
        };

        struct VS_OUT
        {
            float3 vPos : POSITION;
            float2 vUV : TEXCOORD;
            float2 vFullUV : TEXCOORD1;
            float3 vViewPos : POSITION1;        
        };


        VS_OUT VS_LandScape(VTX_IN _in)
        {
            VS_OUT output = (VS_OUT) 0.f;
            
            output.vPos = _in.vPos;
            output.vUV = _in.vUV;
            output.vFullUV = _in.vUV / float2(FaceXCount, FaceZCount);   // uv를 면개수로 나눠서 지형 전체기준의 uv를 저장
            output.vViewPos = mul(float4(output.vPos, 1.f), g_matWV).xyz;
                
            return output;
        }


        // Hull Shader (덮개 쉐이더)
        struct PatchTessFactor // 패치 당 분할 레벨 값
        {
            float EdgeParam[3] : SV_TessFactor;
            float InsideParam : SV_InsideTessFactor;
        };

        // 패치 상수 함수(Patch Constant Function) - 패치당 한번씩 실행되는 함수, 
        // 패치를 어떻게 분할 할 것인지를 반환해 줘야 한다.
        PatchTessFactor HS_PatchConstant(InputPatch<VS_OUT, 3> _Patch, uint _PatchIdx : SV_PrimitiveID)
        {
            PatchTessFactor param = (PatchTessFactor) 0.f;
            
            float3 vViewSidePos = (_Patch[0].vViewPos + _Patch[2].vViewPos) / 2.f;
            float3 vViewUpDownPos = (_Patch[1].vViewPos + _Patch[2].vViewPos) / 2.f;
            float3 vViewSlidePos = (_Patch[0].vViewPos + _Patch[1].vViewPos) / 2.f;
                    
            param.EdgeParam[0] = GetTessFactor(vViewUpDownPos, 1.f, 32.f, 2000.f, 10000.f);
            param.EdgeParam[1] = GetTessFactor(vViewSidePos, 1.f, 32.f, 2000.f, 10000.f);
            param.EdgeParam[2] = GetTessFactor(vViewSlidePos, 1.f, 32.f, 2000.f, 10000.f);
            param.InsideParam = param.EdgeParam[2];
            
            return param;
        }



        [domain("tri")]
        [outputtopology("triangle_cw")]
        [outputcontrolpoints(3)]
        [patchconstantfunc("HS_PatchConstant")]
        [maxtessfactor(64.0)]
        //[partitioning("integer")] 
        [partitioning("fractional_odd")]
        VS_OUT HS_LandScape(InputPatch<VS_OUT, 3> _Patch, uint _Idx : SV_OutputControlPointID, uint _PatchIdx : SV_PrimitiveID)
        {
            VS_OUT output = (VS_OUT) 0.f;
            
            output.vPos = _Patch[_Idx].vPos;
            output.vUV = _Patch[_Idx].vUV;    
            output.vViewPos = _Patch[_Idx].vViewPos;
            output.vFullUV = _Patch[_Idx].vFullUV;

            return output;
        }


        // -----> Tessellator



        // Domain Shader
        struct DS_OUT
        {
            float4 vPosition : SV_Position;
            float2 vUV : TEXCOORD; 
            float2 vFullUV : TEXCOORD1;
            float3 vViewPos : POSITION; 
            
            float3 vViewTangent : TANGENT;
            float3 vViewBinormal : BINORMAL;
            float3 vViewNormal : NORMAL;
        };


        [domain("tri")]
        DS_OUT DS_LandScape(float3 _vLocation : SV_DomainLocation, const OutputPatch<VS_OUT, 3> _Patch, PatchTessFactor _param)
        {
            DS_OUT output = (DS_OUT) 0.f;

            float3 vLocalPos = _Patch[0].vPos * _vLocation[0] + _Patch[1].vPos * _vLocation[1] + _Patch[2].vPos * _vLocation[2];
            output.vUV = _Patch[0].vUV * _vLocation[0] + _Patch[1].vUV * _vLocation[1] + _Patch[2].vUV * _vLocation[2];      
            output.vFullUV = _Patch[0].vFullUV * _vLocation[0] + _Patch[1].vFullUV * _vLocation[1] + _Patch[2].vFullUV * _vLocation[2];
        
            
            // 지형 전체기준 UV 로 전환
            // UV단위값을 구함
            float2 vLandscapeUVStep = float2(1.f / HeightMapResolution.x, 1.f / HeightMapResolution.y);


            // 상하좌우 uv값을 구함
            float2 vLandscapeUV = float2(output.vUV.x / (float)FaceXCount, output.vUV.y / (float)FaceZCount);
            float2 vLandScapeUpUV = float2(vLandscapeUV.x, vLandscapeUV.y - vLandscapeUVStep.y);
            float2 vLandScapeDownUV = float2(vLandscapeUV.x, vLandscapeUV.y + vLandscapeUVStep.y);
            float2 vLandScapeLeftUV = float2(vLandscapeUV.x - vLandscapeUVStep.x, vLandscapeUV.y);
            float2 vLandScapeRightUV = float2(vLandscapeUV.x + vLandscapeUVStep.x, vLandscapeUV.y);

            // 각 정점들이 자기 위치에 맞는 높이값을 높이맵에서 추출 한 후, 자신의 로컬 높이로 지정
            vLocalPos.y = HeightMap.SampleLevel(g_sam_0, vLandscapeUV, 0).r;
            output.vViewPos = mul(float4(vLocalPos, 1.f), g_matWV);
            float2 vLandscapeLocalposStep = float2(FaceXCount / HeightMapResolution.x, FaceZCount / HeightMapResolution.y);
            // vLandscapeLocalposStep는 로컬에서의 1픽셀만큼의 이동을 함.

            
            float3 vLocalUpPos = float3(vLocalPos.x, HeightMap.SampleLevel(g_sam_0, vLandScapeUpUV, 0).r, vLocalPos.z + vLandscapeLocalposStep.y);
            float3 vLocalDownPos = float3(vLocalPos.x, HeightMap.SampleLevel(g_sam_0, vLandScapeDownUV, 0).r, vLocalPos.z - vLandscapeLocalposStep.y);
            float3 vLocalLeftPos = float3(vLocalPos.x - vLandscapeLocalposStep.x, HeightMap.SampleLevel(g_sam_0, vLandScapeLeftUV, 0).r, vLocalPos.z);
            float3 vLocalRightPos = float3(vLocalPos.x + vLandscapeLocalposStep.x, HeightMap.SampleLevel(g_sam_0, vLandScapeRightUV, 0).r, vLocalPos.z);
            
            // Tangent, Binormal, Normal 재계산        
            output.vViewTangent = normalize(mul(float4(vLocalRightPos - vLocalLeftPos, 0.f), g_matWV).xyz);
            output.vViewBinormal = normalize(mul(float4(vLocalUpPos - vLocalDownPos, 0.f), g_matWV).xyz);
            output.vViewNormal = normalize(cross(output.vViewBinormal, output.vViewTangent).xyz);
            
            // 투영좌표계까지 연산
            output.vPosition = mul(float4(vLocalPos, 1.f), g_matWVP);    

            return output;
        }


        struct PS_OUT
        {
            float4 vColor       : SV_Target0;
            float4 vViewNormal  : SV_Target1;
            float4 vViewPos     : SV_Target2;
            float4 vData        : SV_Target3;
        };

        PS_OUT PS_LandScape(DS_OUT _in)
        {
            PS_OUT output = (PS_OUT) 0.f;
            
            output.vColor = float4(0.8f, 0.8f, 0.8f, 1.f);        
            
            float3 vViewNormal = _in.vViewNormal;    
        
            output.vViewPos = float4(_in.vViewPos, 1.f);    
            output.vViewNormal = float4(vViewNormal, 1.f);        
            
            return output;
        }




        #endif