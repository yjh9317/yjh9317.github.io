---
title: 3D Point Light
date: 2022-08-22
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

3D Point Light
====================
* Light의 타입중 한 종류로, Sphere Mesh를 사용한 구 형태의 빛을 비춘다.


<br>

3D Point Light 특징
===========================

* Point Light의 Mesh는 VolumeMesh로 사용하여, VolumeMesh 안에 있는 픽셀을 검사한다.
  * VolumeMesh : 영역을 체크하기 위한 Mesh
  
<br>

* Point Light의 영역안에 들어와도 어떤 픽셀이 호출될지에 대한 검사를 해야한다.
  * 만약 어떤 오브젝트가 범위안에 있어도 광원쪽에 가까운 오브젝트의 단면이 보여야하고 반대쪽은 보이면 안되기 때문.
  * 이 픽셀에 대한 검사는 방법이 2가지
    * Decal을 이용한 영역 체크
    * Stencil를 이용한 영역 체크

<br>

* 여기서는 Stencil은 이론과 Stencil 생성만을 작성하고 Decal을 이용하여 Shader 작성.

<br><br>

Stencil 과정
=======================

1. 후면 Stencil, 전면 Stencil, 사용할 Stencil를 3개 준비한다.<br><br>

2. 먼저 후면 Stencil을 이용하여 깊이가 Sphere의 뒷면보다 앞에 있는 픽셀들을 검출한다.<br><br>

3. 검출한 픽셀들을 1로 대체한다.(여기서는 설명을 위해 1로 대체, 다른값이여도 상관X)<br><br>

4. 전면 Stencil을 이용하여 깊이가 Sphere 앞면보다 뒤에 있고 후면 Stencil에서 스텐실을 1로 변경한 픽셀인지를 검출한다.<br><br>

5. 검출한 픽셀들은 유지한다.(결국 후면Stencil에서 통과한 픽셀들이므로 1로 유지) <br><br>

6. 전면,후면 둘다 통과한 픽셀들은 Stencil값이 1로 채워져 있다. 그 값으로 다른 Stencil에서 Stencil이 1인 픽셀을 검출하고 통과된 픽셀이면 Light RenderTarget에 빛의 영역을 받는 픽셀임을 알리고 0으로 초기화시킨다.

<br><br>

* 주의해야할점
  * 후면 Stencil과 전면 Stencil의 순서가 바뀌면 안된다.
    * 만약 전면 Stencil을 먼저 사용한다면, 카메라가 그 Mesh안에 들어갔을 때 Mesh의 앞면이 카메라보다 뒤쪽에 있으므로 래스터라이저에서 깊이통과자체를 하지 못한다.

<br><br>

Stencil 생성
==================================

<br>


	D3D11_DEPTH_STENCIL_DESC desc = {};

    // 후면 체크
	// RS_TYPE::CULL_FRONT
	desc.DepthEnable = true;                            // 깊이 검사 0
	desc.DepthFunc = D3D11_COMPARISON_GREATER;			// Sphere의 뒷면이 통과되고 그 뒷면으로 픽셀을 체크하는 것이기 때문에 Greater로 설정해야함. 헷갈리기 쉬움

	desc.DepthWriteMask = D3D11_DEPTH_WRITE_MASK_ZERO;	// 깊이 기록 X	

	desc.StencilEnable = true;

	desc.BackFace.StencilFunc = D3D11_COMPARISON_ALWAYS;    // 깊이를 통과하면 Stencil도 통과
	desc.BackFace.StencilPassOp = D3D11_STENCIL_OP_REPLACE; // 통과한 값은 다른 값으로 대체
	desc.BackFace.StencilDepthFailOp = D3D11_STENCIL_OP_ZERO; // 깊이 통과하지 못하면 0으로 초기화
	desc.BackFace.StencilFailOp = D3D11_STENCIL_OP_ZERO;  // 스텐실 통과하지 못하면 0으로 초기화


	// 전면 체크
	// RS_TYPE::CULL_BACK
	desc.DepthEnable = true;                            // 깊이 검사 0
	desc.DepthFunc = D3D11_COMPARISON_LESS;				// Sphere의 앞면이 통과되기 위해 LESS

	desc.DepthWriteMask = D3D11_DEPTH_WRITE_MASK_ZERO;	// 깊이 기록 X	

	desc.StencilEnable = true;

	desc.FrontFace.StencilFunc = D3D11_COMPARISON_EQUAL;    // 후면에서 통과한 값을 이용하기 위해 EQUA 
	desc.FrontFace.StencilPassOp = D3D11_STENCIL_OP_KEEP;   // 통과했다면 Stencil값 유지
	desc.FrontFace.StencilDepthFailOp = D3D11_STENCIL_OP_ZERO; // 깊이 통과하지 못하면 0으로 초기화
	desc.FrontFace.StencilFailOp = D3D11_STENCIL_OP_ZERO;   // 스텐실 통과하지못하면 0으로 초기화



	// 지정된 스텐실 영역만 렌더링
	desc.DepthEnable = false;	    // 스텐실의 값을 체크해서 영역 체크할 것이기 때문에 깊이사용 X
	desc.DepthWriteMask = D3D11_DEPTH_WRITE_MASK_ZERO;	// 깊이 기록 X	

	desc.StencilEnable = true;

	desc.FrontFace.StencilFunc = D3D11_COMPARISON_EQUAL;    // 후면,전면 둘다 통과한 스텐실을 사용
	desc.FrontFace.StencilPassOp = D3D11_STENCIL_OP_ZERO;   // 통과한 Stencil을 Light Target에 저장하고 0으로 초기화
	desc.FrontFace.StencilDepthFailOp = D3D11_STENCIL_OP_ZERO;  // 깊이 통과하지 못하면 0으로 초기화
	desc.FrontFace.StencilFailOp = D3D11_STENCIL_OP_ZERO;   // 스텐실 통과하지못하면 0으로 초기화


<br><br>

Stencil의 특징
=====================
* Depth Stencil 텍스쳐를 이용한 검사
* RenderTarget을 사용하지 않고 Depth Stencil 텍스쳐에서의 Stencil값을 이용하여 영역을 검사한다.
  * 그렇기 때문에 RenderTarget을 빼도 된다.

<br>

* Stencil의 장점
  * 어떤 형태의 Mesh든 상관없이 영역을 체크할 수 있다.

<br>

* Stencil의 단점
  * 렌더링 파이프라인을 3번 돌기 때문에 비용값이 꽤 크다
    * VolumeMesh의 영역체크는 RenderTarget을 사용하지 않아 비용이 크지 않지만,<br>
      기본적인 렌더링 파이프라인을 돌기 위한 연산비용이 2번 더 들기 때문에 비용이 커진다.

<br>


Decal
===================
* 특징
  * 기하학적 계산이 가능한 Mesh에서만 사용이 가능<br><br>
* 과정
  1. 사용하는 Mesh의 World,View 역행렬을 가져온다.(현재 ViewSpace 기준)
  2. 광원 영역인지를 검사할 픽셀을 Mesh의 World,View 역행렬을 곱하여 Mesh의 Local Space로 보낸다.
  3. Local Space로 들어온 픽셀이 Local Space의 Mesh의 내부에 있는지를 체크하여 안에 있다면 Volume Mesh안에 있는 것이므로 빛을 연산한다

<br>


        // ==================
        // Point Light Shader
        // MRT      : Light
        // Mesh     : Sphere
        // RS_TYPE  : CULL_FRONT, 광원영역(Volume Mesh) 안으로 들어왔을 때를 대비
                                  (카메라의 뒤에 Mesh의 앞면이 있으면 안되므로)
        // BS_TYPE  : ONE_ONE, 기존에 그려진 빛(타겟) 을 누적
        // DS_TYPE  : NO_TEST_NO_WRITE

        //#define LightIdx        g_int_0
        //#define PositionTarget  g_tex_0
        //#define NormalTarget    g_tex_1
        // ==================
        VS_DIR_OUT VS_Point(VS_DIR_IN _in)
        {
            VS_DIR_OUT output = (VS_DIR_OUT) 0.f;

            output.vPosition = mul(float4(_in.vPos, 1.f), g_matWVP);
                
            return output;
        }


        PS_DIR_OUT PS_Point(VS_DIR_OUT _in)
        {
            PS_DIR_OUT output = (PS_DIR_OUT) 0.f;
                
            float2 vUV = _in.vPosition.xy / vResolution.xy;
            float3 vViewNormal = NormalTarget.Sample(g_sam_0, vUV).xyz;
            
            // Position이 있는 픽셀의 ViewPos를 가져온다.
            float3 vViewPos = PositionTarget.Sample(g_sam_0, vUV).xyz;    

            // 픽셀에 역행렬을 계산하여 Local Space로 보낸다
            float3 vWorldPos = mul(float4(vViewPos, 1.f), g_matViewInv);
            float3 vLocalPos = mul(float4(vWorldPos, 1.f), g_matWorldInv);
            
            // 여기서는 반지름이 0.5인 Sphere Mesh를 사용하므로 0.5로 내부에 있는지 체크
            if (length(vLocalPos) < 0.5f)
            {
                tLightColor color = (tLightColor) 0.f;
                CalculateLight3D(vViewPos, vViewNormal, LightIdx, color);
                            
                output.vDiffuse = color.vDiff + color.vAmb;
                output.vSpecular = color.vSpec;
                output.vShadowPow = (float4) 0.f;
            
                output.vDiffuse.a = 1.f;
                output.vSpecular.a = 1.f;
                output.vShadowPow.a = 1.f;
            }
            else    // 밖에 있다면 계산X
                clip(-1);    
            
            return output;
        }
