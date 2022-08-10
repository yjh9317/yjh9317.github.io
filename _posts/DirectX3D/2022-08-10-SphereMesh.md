---
title: Sphere Mesh 
date: 2022-08-10
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

Sphere Mesh
===================
* 구형 메쉬
* 구에서 가장 맨위와 가장 맨 아래의 점을 구하고 그 사이에 원 형태의 선을 여러개 만들어서 연결하는 방식
* 구 위의 점은 원점에서 점으로의 방향이 곧 노멀벡터이다.

<br>

* 원리
  * 원점에서 y축이 기준인 180도를 iStackCount만큼 나눈 각도로 선을 그어 구와 만나면 그 지점에 점을 찍고<br>그 점에서 y축에 수직인 선을 긋고 그 선을 y축을 기준으로 원처럼 돌리면 하나의 원이 그려지게 된다.<br>

<br>




        fRadius = 0.5f;

        // Top
        v.vPos = Vec3(0.f, fRadius, 0.f);
        v.vUV = Vec2(0.5f, 0.f);
        v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);
        v.vNormal = v.vPos;
        v.vNormal.Normalize();
        v.vTangent = Vec3(1.f, 0.f, 0.f);
        v.vBinormal = Vec3(0.f, 0.f, 1.f);
        vecVtx.push_back(v);

        // Body
        UINT iStackCount = 40; //    구를 감싸는 선의 개수
        UINT iSliceCount = 40; //    구를 감싸는 선이 가지는 정점의 개수

        float fStackAngle = XM_PI / iStackCount;
        float fSliceAngle = XM_2PI / iSliceCount;

        float fUVXStep = 1.f / (float)iSliceCount;
        float fUVYStep = 1.f / (float)iStackCount;

        for (UINT i = 1; i < iStackCount; ++i)
        {
            float phi = i * fStackAngle;

            for (UINT j = 0; j <= iSliceCount; ++j)
            {
                float theta = j * fSliceAngle;

                v.vPos = Vec3(fRadius * sinf(i * fStackAngle) * cosf(j * fSliceAngle)
                    , fRadius * cosf(i * fStackAngle)
                    , fRadius * sinf(i * fStackAngle) * sinf(j * fSliceAngle));
                v.vUV = Vec2(fUVXStep * j, fUVYStep * i);
                v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);
                v.vNormal = v.vPos;
                v.vNormal.Normalize();

                v.vTangent.x = -fRadius * sinf(phi) * sinf(theta);
                v.vTangent.y = 0.f;
                v.vTangent.z = fRadius * sinf(phi) * cosf(theta);
                v.vTangent.Normalize();

                v.vTangent.Cross(v.vNormal, v.vBinormal);
                v.vBinormal.Normalize();

                vecVtx.push_back(v);
            }
        }

        // Bottom
        v.vPos = Vec3(0.f, -fRadius, 0.f);
        v.vUV = Vec2(0.5f, 1.f);
        v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);
        v.vNormal = v.vPos;
        v.vNormal.Normalize();

        v.vTangent = Vec3(1.f, 0.f, 0.f);
        v.vBinormal = Vec3(0.f, 0.f, -1.f);
        vecVtx.push_back(v);

        // 인덱스
        // 북극점
        for (UINT i = 0; i < iSliceCount; ++i)
        {
            vecIdx.push_back(0);
            vecIdx.push_back(i + 2);
            vecIdx.push_back(i + 1);
        }

        // 몸통
        for (UINT i = 0; i < iStackCount - 2; ++i)
        {
            for (UINT j = 0; j < iSliceCount; ++j)
            {
                // + 
                // | \
                // +--+
                vecIdx.push_back((iSliceCount + 1) * (i)+(j)+1);
                vecIdx.push_back((iSliceCount + 1) * (i + 1) + (j + 1) + 1);
                vecIdx.push_back((iSliceCount + 1) * (i + 1) + (j)+1);

                // +--+
                //  \ |
                //    +
                vecIdx.push_back((iSliceCount + 1) * (i)+(j)+1);
                vecIdx.push_back((iSliceCount + 1) * (i)+(j + 1) + 1);
                vecIdx.push_back((iSliceCount + 1) * (i + 1) + (j + 1) + 1);
            }
        }

        // 남극점
        UINT iBottomIdx = (UINT)vecVtx.size() - 1;
        for (UINT i = 0; i < iSliceCount; ++i)
        {
            vecIdx.push_back(iBottomIdx);
            vecIdx.push_back(iBottomIdx - (i + 2));
            vecIdx.push_back(iBottomIdx - (i + 1));
        }

        pMesh = new CMesh;
        pMesh->Create(vecVtx.data(), (UINT)vecVtx.size(), vecIdx.data(), (UINT)vecIdx.size());
        AddRes(L"SphereMesh", pMesh, true);

        vecVtx.clear();
        vecIdx.clear();
