---
title: Tessellation
date: 2022-09-18
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

Tessellation
==================
* 훌, 도메인 쉐이더를 이용하여 정점 사이에 다른 정점을 대량 생산한다.
* 훌 쉐이더
  * 정점이 호출되는 수만큼 호출되는 쉐이더
  * 만약 RectMesh라면 정점은 4개지만 총 6번 호출 (인덱스버퍼만큼)
  * InputPatch를 사용
    * 훌 쉐이더에서 입력으로 사용할 수 있는 제어점 배열

* 테셀레이터
  * 패치 상수 함수와 정점의 포지션, uv로 정점을 생성
  
* 도메인 쉐이더
  * 테셀레이터에서 만들어진 정점 + 기존의 정점(메쉬의 정점) 사이에 또 다른 정점을 대량 생산하는 쉐이더
  * 테셀레이터에서 만들어진 정점들이 보간되서 들어와야 하지만 보간되지 않기 때문에 패치와 패치 안의 정점에 대한 거리비율을 이용하여 정점의 위치를 정해준다.
    * 새로 생기는 정점은 이 정점을 생성하는데 사용한 패치안의 3개의 정점에서 비율이 높은 정점에 가깝다.
  * 도메인 쉐이더 이후에 래스터라이저로 가야하기 떄문에 이 때 SV_Position 사용해야함
  * 패치 상수 함수의 반환값도 매개변수로 사용할 수 있다.

<br><br>

* 훌 쉐이더 -> 테셀레이터 -> 도메인 쉐이더 순.

<br><br>

테셀레이션 쉐이더 생성
=======================

        // Tessellation Test Shader
        pShader = new CGraphicsShader;

        pShader->SetShaderDomain(SHADER_DOMAIN::DOMAIN_FORWARD);
        pShader->CreateVertexShader(L"Shader\\tessellation.fx", "VS_Tess");
        pShader->CreateHullShader(L"Shader\\tessellation.fx", "HS_Tess");
        pShader->CreateDomainShader(L"Shader\\tessellation.fx", "DS_Tess");
        pShader->CreatePixelShader(L"Shader\\tessellation.fx", "PS_Tess");

        pShader->SetRSType(RS_TYPE::WIRE_FRAME);
        pShader->SetTopology(D3D11_PRIMITIVE_TOPOLOGY::D3D11_PRIMITIVE_TOPOLOGY_3_CONTROL_POINT_PATCHLIST); // 테셀레이션 전용 탑폴로지인 patchlist , 숫자 3은 제어점 개수
        
        AddRes<CGraphicsShader>(L"TessShader", pShader, true);

코드
==================

        #ifndef _TESS
        #define _TESS

        #include "value.fx"

        // ========================
        // Tessellation Test Shader
        // DOMAIN   : Forward
        // RS_TYPE  : Wire Frame
        // DS_TYPE  : Default
        // BS_TYPE  : Default
        // ========================
        struct VTX_IN
        {
            float3 vPos : POSITION;
            float2 vUV : TEXCOORD;
        };

        struct VTX_OUT
        {
            float3 vPos : POSITION;
            float2 vUV : TEXCOORD;
        };

        VTX_OUT VS_Tess(VTX_IN _in)
        {
            VTX_OUT output = (VTX_OUT) 0.f;
            
            output.vPos = _in.vPos;
            output.vUV = _in.vUV;
            
            return output;
        }


        // 패치 상수 함수
        // 패치 당 수행되는 함수
        struct PatchTessFactor
        {
            float EdgeFactor[3] : SV_TessFactor;    // 삼각형을 사용하므로 3
            float InsideFactor : SV_InsideTessFactor;   // 내부에서 분할값
        };

        PatchTessFactor PatchConstFunc(InputPatch<VTX_OUT, 3> _in, uint _patchID : SV_PrimitiveID)
        {
            PatchTessFactor factor = (PatchTessFactor) 0.f;
            
            factor.EdgeFactor[0] = 2.f;         // 정점 0과 마주하는 변을 2등분
            factor.EdgeFactor[1] = 2.f;         // 정점 1과 마주하는 변을 2등분
            factor.EdgeFactor[2] = 2.f;         // 정점 2와 마주하는 변을 2등분 
            factor.InsideFactor = 4.f;          // 내부 꼭짓점 개수
            
            return factor;
        }

        // 훌 쉐이더
        // 정점 당 수행되는 함수
        // 한 정점에서 수행되면 그 정점이 사용된 패치(삼각형)의 정보가 같이 들어온다
        // 대괄호를 이용하여 쉐이더에서 필요한 정보들을 세팅

        [domain("tri")]                         // 삼각형을 사용
        [outputtopology("triangle_cw")]         // 시계방향으로 인덱스 구분
        [outputcontrolpoints(3)]                // 제어점 개수
        [patchconstantfunc("PatchConstFunc")]   // 사용할 패치 상수 함수
        [maxtessfactor(64.f)]                   // 한 면에서 최대 몇개까지 분할
        [partitioning("integer")]               // 분할을 정수단위로 끊을지 실수단위로 끊을지
        // [partitioning("fractional_odd")]  --> 실수 단위로 끊을 때 사용
        VTX_OUT HS_Tess(InputPatch<VTX_OUT, 3> _in          // 패치 구조체
                    , uint _VtxID : SV_OutputControlPointID // 패치 안에서의 정점 ID
                    , uint _patchID : SV_PrimitiveID)       // 패치의 ID,
        {
            VTX_OUT output = (VTX_OUT) 0.f;

            output.vPos = _in[_VtxID].vPos;
            output.vUV = _in[_VtxID].vUV;
            
            return output;
        }

        // Tessellator
        // HullShader 에서 전달한 정보를 토대로 정점을 생성 시키는 단계
        // 생성된 정점을에 대해서 Domain Shader 를 호출 시킨다.

        struct DS_OUT
        {
            float4 vPosition : SV_Position;
            float2 vUV : TEXCOORD;
        };

        // 도메인 쉐이더 후 래스터라이저로 넘겨야 하기 전에 보간을 해줘야 하지만 테셀레이터가 그러한 처리를 해주지 않음.
        // 그렇기에 정점이 만들어지기 위해 사용할 패치와 각 정점의 거리비율값을 이용한다.
        // 도메인에서 받아온 정점의 좌표는 각각의 원본정점* 비율의 합 
        // 3개의 비율의 합은 1

        [domain("tri")]             // 삼각형 사용
        DS_OUT DS_Tess(OutputPatch<VTX_OUT, 3> _OriginPatch, float3 _Ratio : SV_DomainLocation, PatchTessFactor _factor)
        {
            DS_OUT output = (DS_OUT) 0.f;

            float3 vLocalPos =    _OriginPatch[0].vPos * _Ratio[0] 
                                + _OriginPatch[1].vPos * _Ratio[1] 
                                + _OriginPatch[2].vPos * _Ratio[2];
            
            float2 vUV =  _OriginPatch[0].vUV * _Ratio[0]
                        + _OriginPatch[1].vUV * _Ratio[1]
                        + _OriginPatch[2].vUV * _Ratio[2];
            
            output.vPosition = mul(float4(vLocalPos, 1.f), g_matWVP);
            output.vUV = vUV;
            
            
            return output;
        }




        float4 PS_Tess(VTX_OUT _in) : SV_Target
        {
            float4 vOutColor = float4(1.f, 0.f, 1.f, 1.f);    
            return vOutColor;
        }

        #endif
