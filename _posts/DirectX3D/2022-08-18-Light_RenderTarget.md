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
* Forward 에서는 Lighting을 처리할 때는 물체가 기준이므로 각 물체의 Shader에서 광원 개수만큼 빛을 처리했지만,<br>
  Deferred 에서는 광원이 기준이므로 Deferred의 텍스쳐들을 이용하여 빛의 영역을 따져서 영역 안에 있는 픽셀이 호출되는지를 검사하고 빛을 처리한다.

<br>

* Light RenderTarget 종류
  * Diffuse(자연광)
  * Specular(반사광)
  * ShadowPower(그림자 세기)

<br>

* LightType종류
  * Directional(태양광)
    * RectMesh로 화면 전체 픽셀을 호출한다.
  * Point (전구,횃불같은 빛)
    * SphereMesh를 이용한다.
  * Spot (손전등) 
    * ConeMesh를 이용한다.

<br>

Light RenderTarget 생성
=================================
 * Deferred와 마찬가지로 렌더타겟 및 리소스로 사용(D3D11_BIND_RENDER_TARGET | D3D11_BIND_SHADER_RESOURCE)
 * Diffuse,Specular는 빛의 색상을 저장하기 위해 R8G8B8A8_UNORM 사용
 * ShadowPower는 그림자의 세기를 저장하기 위해 R32_Float를 사용
<br>

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
* Directional Light는 화면에 있는 모든 픽셀을 호출 하기 위해 RectMesh를 사용한다.<br><br>
* Point Light의 Mesh는 VolumeMesh로 , SphereMesh 안에 있는 픽셀을 호출한다.
  * VolumeMesh : 영역을 체크하기 위한 Mesh
* Point Light의 영역안에 들어와도 픽셀이 호출될지에 대한 검사를 해야한다.
  * 만약 어떤 오브젝트가 범위안에 있어도 광원쪽에 가까운 오브젝트의 단면이 보여야하고 반대쪽은 보이면 안되기 때문.
  * 이 픽셀에 대한 검사는 방법이 2가지
    * 기하학적 메쉬를 이용한 검사
    * Stencil버퍼를 이용한 검사