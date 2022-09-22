---
title: LandScape
date: 2022-09-22
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

3D Collision
================
* 3차원에서의 두 물체간의 충돌체크
* AABB 충돌과 OBB 충돌이 있다.

<br><br>

AABB
========================

Sphere
---------------------
* Sphere의 AABB는 두 물체의 중심점으로부터의 거리와 두 물체의 반지름을 비교하여 충돌체크
* 두 물체의 중심점으로부터의 거리가 두 물체의 반지름의 합보다 크다면 충돌X
  
<br>

        bool CCollisionMgr::IsCollision_Sphere(CCollider3D* _pLeftCol, CCollider3D* _pRightCol)
        {
            Vec3 vCenter = _pLeftCol->GetWorldPos() - _pRightCol->GetWorldPos();
            float fDist = vCenter.Length();
            
            float fRadius = fabsf(_pLeftCol->GetWorldScale().x) * 0.5f + fabsf(_pRightCol->GetWorldScale().x) * 0.5f;

            if (fRadius < fDist)
            {
                return false;
            }

            return true;
        }

<br><br>

Cube
---------------------
* Cube의 AABB는 각 축 모서리의 길이와 두 물체의 거리를 비교하여 충돌체크
  * Sphere처럼 두 물체의 반지름(모서리의 절반)의 합과 거리를 비교함
* 두 물체의 거리와 모서리의 길이를 비교하여 만약 한 축이라도 물체의 거리가 멀다면 그 축에서 두 물체가 만나지 않으므로 충돌X

<br><br>

        bool CCollisionMgr::IsCollision_Cube(CCollider3D* _pLeftCol, CCollider3D* _pRightCol)
        {
            Vec3 vCenter = _pRightCol->GetWorldPos() - _pLeftCol->GetWorldPos();

            float fXDist =_pRightCol->GetWorldPos().x - _pLeftCol->GetWorldPos().x;
            float fYDist = _pRightCol->GetWorldPos().y - _pLeftCol->GetWorldPos().y;
            float fZDist = _pRightCol->GetWorldPos().z - _pLeftCol->GetWorldPos().z;

            //float fDist = vCenter.Length();

            Vec3 vLeftScale = _pLeftCol->GetWorldScale();
            Vec3 vRightScale = _pRightCol->GetWorldScale();

            if (abs(_pLeftCol->GetWorldScale().x * 0.5f + _pLeftCol->GetWorldScale().x * 0.5f) < abs(fXDist) ||
                abs(_pLeftCol->GetWorldScale().y * 0.5f + _pLeftCol->GetWorldScale().y * 0.5f) < abs(fYDist) ||
                    abs(_pLeftCol->GetWorldScale().z * 0.5f + _pLeftCol->GetWorldScale().z * 0.5f) < abs(fZDist))
            {
                return false;
            }

            return true;
        }


<br><br>

OBB 충돌
===================

* 둘다 CubeMesh를 사용했을 때만 비교
* 두 물체의 거리와 면끼리의 비교 , 두 물체의 축끼리의 외적을 구해서 거리를 비교한 다음 거리 체크하여 구한다
* 두 물체사이의 분리축이 있는지를 체크하고 만약 있다면 충돌하지 않는다.


<br><br>

        bool CCollisionMgr::IsCollision_OBB(CCollider3D* _pLeftCol, CCollider3D* _pRightCol)
        {
            // 충돌체가 사용하는 기본 도형(사각형) 로컬 정점위치를 알아낸다.
            //    0 ㅡ 1
            //   /|   /|
            //  3 ㅡ 2 |	
            //  | 7 '|'/6
            //  |____|/
            //  4    5  
            //
            // Cube 메쉬의 정점위치

            // 메쉬로부터 정점의 로컬 위치를 저장
            static CMesh* pCubeMesh = CResMgr::GetInst()->FindRes<CMesh>(L"CubeMesh").Get();
            static Vtx* pVtx = pCubeMesh->GetVtxSysMem();
            static Vec3 vLocalPos[8] = { pVtx[0].vPos, pVtx[1].vPos, pVtx[2].vPos, pVtx[3].vPos,
                                        pVtx[4].vPos, pVtx[5].vPos, pVtx[6].vPos, pVtx[7].vPos };

            Matrix matLeft = _pLeftCol->GetWorldMat();
            Matrix matRight = _pRightCol->GetWorldMat();

            // Local 스페이스의 여섯 개의 정점을 각 충돌체의 월드행렬을 곱해서 월드 위치로 보낸다.
            Vec3 vAsix[6] = {};


            // (Vector3, 1.f) X Matirx(4x4)
            // 월드로 보낸 정점을 통해서 각 투영 축 이면서 투영시킬 벡터 성분 6개를 구한다.
            vAsix[0] = XMVector3TransformCoord(vLocalPos[1], matLeft) - XMVector3TransformCoord(vLocalPos[0], matLeft);
            vAsix[1] = XMVector3TransformCoord(vLocalPos[3], matLeft) - XMVector3TransformCoord(vLocalPos[0], matLeft);
            vAsix[2] = XMVector3TransformCoord(vLocalPos[1], matRight) - XMVector3TransformCoord(vLocalPos[0], matRight);
            vAsix[3] = XMVector3TransformCoord(vLocalPos[3], matRight) - XMVector3TransformCoord(vLocalPos[0], matRight);
            vAsix[4] = XMVector3TransformCoord(vLocalPos[7], matLeft) - XMVector3TransformCoord(vLocalPos[0], matLeft);
            vAsix[5] = XMVector3TransformCoord(vLocalPos[7], matRight) - XMVector3TransformCoord(vLocalPos[0], matRight);


            // 월드에 배치된 두 충돌체의 중심을 이은 벡터
            //Vec3 vCenter = XMVector3TransformCoord(Vec3::Zero, matRight) - XMVector3TransformCoord(Vec3::Zero, matLeft);	

            // 두 물체의 중심간의 거리
            Vec3 vCenter = _pRightCol->GetWorldPos() - _pLeftCol->GetWorldPos();

            // 각 물체의 면에 해당하는 축에 투영하여 중심간의 거리와 거리값을 비교

            for (int i = 0; i < 6; ++i)
            {
                Vec3 vProj = vAsix[i];
                vProj.Normalize();

                float fDist = 0.f;

                for (int j = 0; j < 6; ++j)
                {
                    // vProj 에 vAsix[j] 를 투영시킨 길이		
                    fDist += abs(vAsix[j].Dot(vProj));
                }
                fDist *= 0.5f;
                float fCenterDist = abs(vCenter.Dot(vProj));

                if (fDist < fCenterDist)
                    return false;
            }

            // 왼쪽 충돌체의 축과 오른쪽 충돌체의 축을 설정
            Vec3 _LeftAxis[3] = { XMVector3TransformCoord(vLocalPos[1], matLeft) - XMVector3TransformCoord(vLocalPos[0], matLeft) ,	 // x축
                                XMVector3TransformCoord(vLocalPos[3], matLeft) - XMVector3TransformCoord(vLocalPos[0], matLeft) ,	 // z축
                                XMVector3TransformCoord(vLocalPos[7], matLeft) - XMVector3TransformCoord(vLocalPos[0], matLeft) }; // y축

            Vec3 _RightAxis[3] = { XMVector3TransformCoord(vLocalPos[1], matRight) - XMVector3TransformCoord(vLocalPos[0], matRight) ,  // x축
                                XMVector3TransformCoord(vLocalPos[3], matRight) - XMVector3TransformCoord(vLocalPos[0], matRight) ,  // z축
                                XMVector3TransformCoord(vLocalPos[7], matRight) - XMVector3TransformCoord(vLocalPos[0], matRight) }; // y축

            // 두 물체의 축을 하나씩 받아와서 외적으로 수직인 벡터를 구하고 그 벡터에 투영해서 중심간의 거리와 거리값을 비교

            for (int i = 0; i < 3; ++i)
            {

                for (int j = 0; j < 3; ++j)
                {
                    float fCenterDist;
                    float fDist = 0.f;
                    Vec3 Left = _LeftAxis[i];
                    Vec3 Right = _RightAxis[j];
                    Vec3 vCross = Left.Cross(Right);	

                    fDist += abs(vCross.Dot(_LeftAxis[0]));
                    fDist += abs(vCross.Dot(_LeftAxis[1]));
                    fDist += abs(vCross.Dot(_LeftAxis[2]));
                    fDist += abs(vCross.Dot(_RightAxis[0]));
                    fDist += abs(vCross.Dot(_RightAxis[1]));
                    fDist += abs(vCross.Dot(_RightAxis[2]));


                    fCenterDist = abs(vCenter.Dot(vCross));

                    if (fDist < fCenterDist)
                        return false;
                }

            }
            return true;
        }