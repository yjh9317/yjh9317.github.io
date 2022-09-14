---
title: Frustum Culling
date: 2022-09-14
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---


<br>

Frustum culling(절두체 컬링)
========================
* 카메라의 시야범위 안에 있는 오브젝트들에 한해 렌더링을 돌려 최적화를 하는 방법
* 절두체는 사각뿔에서 밑면에 평행한 면을 잘라서 만든 도형으로 총 6면이 있다.

* 6면
  * NEAR  : 카메라에서 가장 가까운 곳의 시야 범위 면
  * FAR   : 카메라에서 가장 먼 곳의 시야 범위 면
  * LEFT  : 카메라의 좌측 시야 범위 면
  * RIGHT : 카메라의 우측 시야 범위 면
  * UP    : 카메라의 상단 시야 범위 면
  * DOWN  : 카메라의 하단 시야 범위 면


<br>



원리
============
* 평면의 방정식 ax  by + cz + d = 0 에서 법선벡터(a,b,c)와 평면까지의 거리 d를 이용하여 임의의 점이 절두체 안에 들어있는지 확인하는 방식

* 오브젝트의 중심 점에서 평면의 법선벡터와 내적하여 평면까지의 거리를 구한 후 그 거리값을 이용하여 모든 면에 대해 통과한다면 렌더링을 진행한다.
  * 내적을 이용하여 오브젝트의 중심점이 절두체 안에 있는지 확인하는 방법
  * PointCheck함수

<br>

* 만약 오브젝트의 중심은 절두체 밖으로 빠져나갔지만 오브젝트의 끝쪽이 절두체에 들어올 경우 렌더링이 되지 않는다.
  * 이럴 경우에는 오브젝트를 감싸는 구(Bounding Box)의 반지름만큼 보정하면 중심점은 밖에 있어도 반지름만큼 안에 들어간다면 오브젝트는 렌더링이 되는 방식이다.
    * BoundigBox : 오브젝트를 완전히 감싸는 구, 완전히 감싸야 하기 때문에 중심점에서 반지름값만큼 더한값이 항상 오브젝트의 크기보다 크거나 같다.
    * SphereCheck함수




과정
===================
1. 미리 투영된 좌표를 준비<br><br>
2. 투영된 좌표를 절두체 컬링을 적용할 카메라의 투영 역행렬, 뷰 역행렬을 곱해 월드로 보냄<br><br>
3. 그렇게 월드좌표로 보내진 좌표들을 이용하여 카메라의 절두체 면에 사용할 평면을 만듦<br><br>
4. 그렇게 만들어진 6개의 면으로 오브젝트의 중심점과 내적하여 절두체 안에 있으면 렌더링하고 하나라도 면에서 밖에 있다면 렌더링하지 않는다.


<br>

코드
======================



        CFrustum::CFrustum()
            : m_ProjPos{}
            , m_WorldPos{}
            , m_arrPlane{}
            , m_pCam(nullptr)
        {
            //   4 ---- 5
            //  /|    / |
            // 0 -- 1   |
            // | 6 -|-- 7
            // 2 -- 3/

            // 미리 투영 좌표를 설정
            
            m_ProjPos[0] = Vec3(-1.f, 1.f, 0.f);
            m_ProjPos[1] = Vec3(1.f, 1.f, 0.f);
            m_ProjPos[2] = Vec3(-1.f, -1.f, 0.f);
            m_ProjPos[3] = Vec3(1.f, -1.f, 0.f);

            m_ProjPos[4] = Vec3(-1.f, 1.f, 1.f);
            m_ProjPos[5] = Vec3(1.f, 1.f,	1.f);
            m_ProjPos[6] = Vec3(-1.f, -1.f,1.f);
            m_ProjPos[7] = Vec3(1.f, -1.f, 1.f);
        }

        CFrustum::~CFrustum()
        {
        }

        void CFrustum::finalupdate()
        {

            // Frustum 을 소유하고 있는 카메라의 Proj 역행렬, View 역행렬 을 가져온다.
            const Matrix& matViewInv = m_pCam->GetViewInvMat();
            const Matrix& matProjInv = m_pCam->GetProjInvMat();

            // VP 역행렬을 곱해서 WorldPos 를 구한다.
            Matrix matVPInv = matProjInv * matViewInv;

            for (int i = 0; i < 8; ++i)
            {
                m_WorldPos[i] = XMVector3TransformCoord(m_ProjPos[i], matVPInv);
            }

            // 8개의 월드 좌표를 이용해서 월드상에서 절두체를 구성하는 6개의 평면을 정의한다.
            //   4 ---- 5
            //  /|    / |
            // 0 -- 1   |
            // | 6 -|-- 7
            // 2 -- 3/
            m_arrPlane[(UINT)PLANE::PL_LEFT] = XMPlaneFromPoints(m_WorldPos[4], m_WorldPos[0], m_WorldPos[2]);
            m_arrPlane[(UINT)PLANE::PL_RIGHT] = XMPlaneFromPoints(m_WorldPos[1], m_WorldPos[5], m_WorldPos[7]);

            m_arrPlane[(UINT)PLANE::PL_UP] = XMPlaneFromPoints(m_WorldPos[0], m_WorldPos[4], m_WorldPos[5]);
            m_arrPlane[(UINT)PLANE::PL_DOWN] = XMPlaneFromPoints(m_WorldPos[2], m_WorldPos[3], m_WorldPos[7]);

            m_arrPlane[(UINT)PLANE::PL_NEAR] = XMPlaneFromPoints(m_WorldPos[2], m_WorldPos[0], m_WorldPos[1]);
            m_arrPlane[(UINT)PLANE::PL_FAR] = XMPlaneFromPoints(m_WorldPos[7], m_WorldPos[5], m_WorldPos[4]);
        }

        bool CFrustum::PointCheck(Vec3 _vPos)
        {
            for (int i = 0; i < (UINT)PLANE::END; ++i)
            {
                // N dot P = D
                float fDot = _vPos.Dot(m_arrPlane[i]);
                if (fDot + m_arrPlane[i].w > 0)
                {
                    return false;
                }
            }

            return true;
        }

        bool CFrustum::SphereCheck(Vec3 _vPos, float _fRadius)
        {
            for (int i = 0; i < (UINT)PLANE::END; ++i)
            {
                // N dot P = D
                float fDot = _vPos.Dot(m_arrPlane[i]);
                if (fDot + m_arrPlane[i].w > _fRadius)
                {
                    return false;
                }
            }

            return true;
        }
