---
title: SwapChain
date: 2022-06-01
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---



SwapChain
=============
DirectX에서는 SwapChain이란 것으로 버퍼 두장으로(프론트,백) 프론트 버퍼에서는 화면을 출력하고 있다가  
백 버퍼에서 그림이 완성되면 Present라는 함수를 호출하여 프론트 버퍼와 백 버퍼를 서로 뒤바꾸고
프론트 버퍼로 뒤바뀐 백 버퍼를 화면에 출력하는 방식이다.  
  
<br><br>

Code
================

<br>

 	DXGI_SWAP_CHAIN_DESC desc = {}; //SwapChain을 사용하기 위한 구조체

	desc.BufferCount = 1;                                   // 1을 넣어주면 알아서 내부적으로 프론트,백버퍼를 만든다.
	desc.BufferDesc.Width= (UINT)m_vRenderResolution.x;     // 만들어진 버퍼의 가로
	desc.BufferDesc.Height = (UINT)m_vRenderResolution.y;	  // 만들어진 버퍼의 세로
	desc.BufferDesc.Format = DXGI_FORMAT_R8G8B8A8_UNORM;  // 만들어진 픽셀의 Format (R:8bit, G:8bit , B:8bit, A:8bit 로 총 4byte)
  
    RefreshRate는 모니터가 화면을 업데이트하는 주기로 분자/분모값으로 60이면 1초에 60번이다.
  
	desc.BufferDesc.RefreshRate.Denominator = 1;        	//  분모
	desc.BufferDesc.RefreshRate.Numerator = 60;	        	//  분자
	desc.BufferDesc.Scaling = DXGI_MODE_SCALING::DXGI_MODE_SCALING_UNSPECIFIED; //디폴트 옵션
	desc.BufferDesc.ScanlineOrdering = DXGI_MODE_SCANLINE_ORDER::DXGI_MODE_SCANLINE_ORDER_UNSPECIFIED; //픽셀 쉐이더의 픽셀 순서(디폴트)

	desc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT; // 렌더 타겟 용도를 알림
	desc.Flags = 0; //옵션없이 디폴트
	desc.SwapEffect = DXGI_SWAP_EFFECT_DISCARD; // SwapChain으로 프론트버퍼에서 백버퍼로 바뀔때 프론트버터 일 때 남겨진 그림을 지움.
	
	desc.SampleDesc.Count = 1;    //  디폴트
	desc.SampleDesc.Quality = 0;  //  디폴트
		
	desc.OutputWindow= m_hWnd;  //  출력하려는 목적지 윈도우
	desc.Windowed = true;       //  창모드거나 전체화면 (true면 창,false면 전체화면)





	//아래는 관용적으로 스왑체인을 만드는 구조

	ComPtr<IDXGIDevice> pDXGIDevice = nullptr;
	ComPtr<IDXGIAdapter> pDXGIAdaptor = nullptr;
	ComPtr<IDXGIFactory> pDXGIFactory = nullptr;

	//사용자 정의 타입은 만들어질때 자동으로 아이디값이 생성되고 그 아이디를 찾아오는 QueryInterFace
	//void포인터인 이유는 함수마다 타입이 다를 수 있기 때문

	m_pDevice->QueryInterface(__uuidof(IDXGIDevice),(void**)pDXGIDevice.GetAddressOf());
	pDXGIDevice->GetParent(__uuidof(IDXGIAdapter), (void**)pDXGIAdaptor.GetAddressOf());
	pDXGIAdaptor->GetParent(__uuidof(IDXGIFactory), (void**)pDXGIFactory.GetAddressOf());
	
	pDXGIFactory->CreateSwapChain(m_pDevice.Get(),&desc,m_pSwapChain.GetAddressOf());
	//원본 디바이스 .Get()은 최상위 부모타입,스왑체인 구조체 주소값,SwapChain의 주소
