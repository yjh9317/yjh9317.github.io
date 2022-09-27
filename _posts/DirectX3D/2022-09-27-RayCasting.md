---
title: RayCasting
date: 2022-09-27
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

RayCasting
================
* 가상의 공간에 보이지 않는 빛(Ray)을 투사해 빛에 닿는 표면을 파악하는 기술
* 카메라를 기준으로 카메라와 카메라와 바라보는 방향쪽의 오브젝트의 거리값을 계산
* RayCasting을 이용하여 카메라가 바라보는 지형(LandScape)쪽의 값을 변경하여 높이를 실시간으로 높이를 변경할 수 있다.
* ComputeShader를 이용


<br>

쉐이더
=================

        // Raycast 결과를 받을 구조체
        struct tRaycastOut
        {
            Vec2 vUV;       // 지형 전체 기준의 UV
            int  iDist;     // 클릭 지점과 카메라 간의 거리
            int  bSuccess;  // 성공여부
        };

        -------------------------------------------------

        #ifndef _RAYCAST
        #define _RAYCAST

        #include "value.fx"
        #include "func.fx"

        RWStructuredBuffer<tRaycastOut> OUTPUT : register(u0);  // 결과를 받을 구조화버퍼

        // 리소스
        #define HEIGHT_MAP      g_tex_0     // 높이맵

        #define CAM_POS         g_vec4_0    // 카메라 위치
        #define CAM_DIR         g_vec4_1    // 카메라 방향

        #define FACE_X          g_int_0     // x축의 면 개수
        #define FACE_Z          g_int_1     // z축의 면 개수

        [numthreads(32, 32, 1)]
        void CS_Raycast(int3 _iThreadID : SV_DispatchThreadID)
        {
            int2 id = _iThreadID.xy;

            // LandScape 밖으로 나갔다면 return
            if (FACE_X * 2 <= id.x || FACE_Z <= id.y)
            {
                return;
            }    
            
            float3 vPos[3] = { (float3) 0.f, (float3) 0.f, (float3) 0.f };

            if (0 == id.x % 2)
            {
                // 아래쪽 삼각형        
                // 2
                // | \
                // 0--1        
                vPos[0].x = id.x / 2;
                vPos[0].z = id.y;

                vPos[1].x = vPos[0].x + 1;
                vPos[1].z = id.y;

                vPos[2].x = vPos[0].x;
                vPos[2].z = id.y + 1;
            }
            else
            {
                // 윗쪽 삼각형
                // 1--0
                //  \ |
                //    2  
                vPos[0].x = (id.x / 2) + 1;
                vPos[0].z = id.y + 1;

                vPos[1].x = vPos[0].x - 1;
                vPos[1].z = id.y + 1;

                vPos[2].x = vPos[0].x;
                vPos[2].z = id.y;
            }

            // uv를 계산해서 높이맵에서 해당 지점의 높이값을 추출
            for (int i = 0; i < 3; ++i)
            {
                float2 uv = float2(saturate(vPos[i].x / (float)FACE_X), saturate(1.f - vPos[i].z / (float)FACE_Z));
                vPos[i].y = HEIGHT_MAP.SampleLevel(g_sam_0, uv, 0).x;
            }

            float3 vCrossPoint = (float3) 0.f;
            float fDist = 0.f;

            // IntersectsLay : 수학을 이용한 광선 충돌 체크
            if (IntersectsLay(vPos, CAM_POS.xyz, CAM_DIR.xyz, vCrossPoint, fDist))
            {
                int iDist = (int)(10000.f * fDist);
                int iDistOut = 0;

                InterlockedMin(OUTPUT[0].iDist, iDist, iDistOut);

                if (iDistOut < iDist)
                {
                    // 실패
                    return;
                }

                OUTPUT[0].vUV = float2(saturate(vCrossPoint.x / (float)FACE_X), saturate(1.f - vCrossPoint.z / (float)FACE_Z));
                OUTPUT[0].success = 1;
            }
        }


IntersectsLay 함수
----------------------

        int IntersectsLay(float3 _vertices[3], float3 _vStart, float3 _vDir, out float3 _vCrossPoint, out float _fResult)
        {
            float3 edge[2] = { (float3) 0.f, (float3) 0.f };
            edge[0] = _vertices[1].xyz - _vertices[0].xyz;
            edge[1] = _vertices[2].xyz - _vertices[0].xyz;

            float3 normal = normalize(cross(edge[0], edge[1]));
            float b = dot(normal, _vDir);

            float3 w0 = _vStart - _vertices[0].xyz;
            float a = -dot(normal, w0);
            float t = a / b;

            _fResult = t;

            float3 p = _vStart + t * _vDir;

            _vCrossPoint = p;

            float uu, uv, vv, wu, wv, inverseD;
            uu = dot(edge[0], edge[0]);
            uv = dot(edge[0], edge[1]);
            vv = dot(edge[1], edge[1]);

            float3 w = p - _vertices[0].xyz;
            wu = dot(w, edge[0]);
            wv = dot(w, edge[1]);
            inverseD = uv * uv - uu * vv;
            inverseD = 1.0f / inverseD;

            float u = (uv * wv - vv * wu) * inverseD;
            if (u < 0.0f || u > 1.0f)
            {
                return 0;
            }

            float v = (uv * wu - uu * wv) * inverseD;
            if (v < 0.0f || (u + v) > 1.0f)
            {
                return 0;
            }

            return 1;
        }



        #endif