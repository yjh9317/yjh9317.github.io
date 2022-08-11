---
title: Normal Mapping
date: 2022-08-11
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---


노말 맵핑
==============================
* 평평한 Mesh를 쓰면 노멀벡터가 전부 같아서 명암이 모두 같아 부자연스러워진다.
* 울퉁불퉁한 Mesh를 사용하기 위해 정점을 찍어야하는데 디테일해질수록 정점이 많아지면서 렌더링 부담이 심해진다
  * 예시 : terrain wireframe

* 그래서 적은 정점으로 디테일을 살리기 위해 나온 것이 노말 맵핑이다.
* 노말 맵핑이란 노말 맵을 사용해서 명암을 표현하여 입체감과 질감을 구현하는 방법이다.

<br><br>

노말 맵핑 원리
===============
노말 맵은 노말 맵핑을 적용시킬 텍스쳐의 노말 벡터를 RGB에 저장한 텍스쳐이다.(노말 벡터의 xyz축 순서대로 RGB에 저장함)<br><br>
이 노말 맵 안에 있는 노말 벡터를 가져와서 그 값으로 명암을 적용시켜 입체감을 구현하는 것인데<br>
3D에서는 로컬 스페이스의 좌표가 바뀌면서 애니메이션을 적용 시키는데 노말 맵은 고정된 로컬 스페이스의 노말 벡터가 저장되어 있기 때문에 맞지 않게 된다.


그래서 정점의 노말벡터가 z축인 정점별의 고유 좌표계인 Tangent Space(접선 공간)로 가져와서 상대적인 좌표로 변환시켜야 한다.<br>

<br><br>

노말 벡터를 RGB에 저장하는 과정
================
노말 벡터를 RGB로 변환해야 하는데 여기서 생각 해야할 것이 있다.<br><br>

* 첫 번째<br>
  
셰이더에서 사용하기 위해서 노말 벡터를 RGB로 변환하는데 Shader는 텍스쳐를 샘플링할 때 UV값으로 0 에서 1 사이의 실수값으로 변환해서 가져온다.<br>

노말 벡터는 방향값을 가지므로 -1 부터 1의 값인데 이 값이 0 ~ 1값으로 치환이 되는것 이므로 <br>
음수인 -1 ~ 0 의 값은 0에서 0.5로 , 양수인 0 ~ 1의 값은 0.5에서 1의 값으로 치환이 된다.

샘플링한 UV값을 다시 원래대로 -1 ~ 1사이의 값(진짜 노말벡터방향)으로 되돌리기 위해 0 ~ 1 인 값에 *2를 하고 -1를 해서 다시 범위를 -1 ~ 1 로 되돌려준다.

<br>

* 두 번째<br>

노말 맵의 노말 벡터를 Tangent Space로 적용시켜야 한다.그러기 위해 회전 행렬을 구해야하는데

구하는 방법은 Tangent Space에서의 단위벡터가 노말맵의 Tangent,Normal,Binormal의 값과 일치시키는 것이다.<br>

일단 노말 맵의 Tangent, Normal, Binormal의 값을 따로 저장한다음 비교를 한다.<br>
Tangent Space에서의 (1,0,0)이 정점의 tangent 와 같고<br>
Tangent Space에서의 (0,0,1)이 정점의 Binoraml과 같고<br>
Tangent Space에서의 (0,1,0)이 정점의 Normal과 같다.<br>

이 정보들을 토대로 행렬로 변환시켜 보면<br>

    ( 1 , 0 , 0 )                 ( Tx, Ty, Tz )
    ( 0 , 1 , 0 ) * R(회전행렬) = ( Nx, Ny, Nz )
    ( 0 , 0 , 1 )                 ( Bx, By, Bz )

<br>
이 모양이 나오게 되는데 왼쪽 행렬은 단위 행렬이므로 곧 R(회전 행렬)이 계산 값이 되는 것이다.<br>
이 회전 행렬을 곱하기 전에 주의 해야 할 점은 DirectX의 좌표계와 Tangent Space의 좌표계의 축이 다르기 떄문에<br> Normal과 Binormal의 위치를 바꿔줘야 한다.<br>

* DirectX의 y와 z축를 바꿔야 Tangent Space의 좌표축이 된다.<br><br>

그래서 결국 회전 행렬은 아래가 된다.<br>

    ( Tx, Ty, Tz )
    ( Bx, By, Bz )
    ( Nx, Ny, Nz )



 

<br>

코드
=======================

      // 노말맵핑
      if (g_btex_1) // 노말 맵이 있다면
      {
          float3 vNormal = g_tex_1.Sample(g_sam_0, _in.vUV).rgb;
          vNormal = vNormal * 2.f - 1.f;  // 0 ~ 1 값을 -1 ~ 1 로 확장       
          
          // 회전 행렬
          float3x3 matRot =
          {
                _in.vViewTangent
              , _in.vViewBinormal
              , _in.vViewNormal
          };
          
          // 뷰 스페이스 기준의 노말 벡터와 회전 행렬을 곱해서 뷰 스페이스의 노말벡터를 구함.
          vViewNormal = normalize(mul(vNormal, matRot));        
      }




<br><br>

Tangent Space(접선 공간)를 사용하는 이유
=================================
3D에서는 물체의 정보(로컬 스페이스의 좌표값)가 수시로 바뀌면서 노말 벡터도 수시로 변하기 때문에 
매 프레임 노말 벡터를 계산한다면 새로운 계산이 필요로 한다.<br>  

그런데 Tangent Space를 사용하는 이유는 결국 정점의 표면 정보를 기준으로 하는 상대좌표이기 때문에 노말 방향이 변하더라도 상관이 없게 된다.


<br><br>

참고 : https://mgun.tistory.com/1289<br>
       https://artkong.tistory.com/39<br>
       https://shkim0811.tistory.com/45
