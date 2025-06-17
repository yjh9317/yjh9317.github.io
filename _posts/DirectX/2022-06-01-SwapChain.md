---
title: SwapChain
date: 2022-06-01
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---



# **DirectX 스왑 체인 (SwapChain)**

* DirectX에서 `스왑 체인(SwapChain)`은 부드러운 화면 출력을 위한 핵심 기술*

* 기본적으로 두 개의 버퍼, 즉 `프론트 버퍼(Front Buffer)`와 `백 버퍼(Back Buffer)`를 사용

  * 프론트 버퍼: 현재 모니터에 보여지고 있는 화면
  * 백 버퍼: 다음 화면에 보여질 내용을 그리는 작업 공간

<br>

# **렌더링 과정**

1. 애플리케이션은 백 버퍼에 다음 프레임의 이미지를 그림

2. 그림이 완성되면, `Present()` 함수를 호출

3. Present() 함수는 프론트 버퍼와 백 버퍼를 서로 `교체(swap)` 즉, 그림이 완성된 기존의 백 버퍼가 새로운 프론트 버퍼가 되어 화면에 표시되고, 기존의 프론트 버퍼는 새로운 백 버퍼가 되어 다음 그림을 그릴 준비



* 이러한 더블 버퍼링(Double Buffering) 방식을 통해, 화면에 그림이 그려지는 중간 과정을 사용자가 보지 않게 되어 화면 깜빡임(flickering) 없이 부드러운 애니메이션을 구현

<br>

# **스왑 체인 설정 코드 (DXGI_SWAP_CHAIN_DESC)**


```c++
DXGI_SWAP_CHAIN_DESC desc = {}; // 스왑 체인 설정을 위한 구조체 초기화

// 버퍼 설정
desc.BufferCount = 1; // 일반적으로 1을 설정하면 DirectX가 프론트 버퍼와 백 버퍼를 자동으로 관리합니다.
                      // (실제로는 백 버퍼의 개수를 의미하며, 프론트 버퍼는 항상 하나입니다)
desc.BufferDesc.Width = (UINT)m_vRenderResolution.x;  // 생성될 버퍼의 가로 해상도
desc.BufferDesc.Height = (UINT)m_vRenderResolution.y; // 생성될 버퍼의 세로 해상도
desc.BufferDesc.Format = DXGI_FORMAT_R8G8B8A8_UNORM; // 픽셀 포맷 (예: RGBA 각 채널당 8비트, 총 32비트)

// 화면 주사율 (Refresh Rate) 설정
// 모니터가 1초에 화면을 업데이트하는 횟수입니다. (예: 60/1 = 60Hz)
desc.BufferDesc.RefreshRate.Denominator = 1;    // 분모
desc.BufferDesc.RefreshRate.Numerator = 60;     // 분자

// 기타 버퍼 설정
desc.BufferDesc.Scaling = DXGI_MODE_SCALING_UNSPECIFIED; // 스케일링 방식 (기본값)
desc.BufferDesc.ScanlineOrdering = DXGI_MODE_SCANLINE_ORDER_UNSPECIFIED; // 스캔라인 순서 (기본값)

// 스왑 체인 동작 설정
desc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT; // 버퍼가 렌더 타겟으로 사용됨을 명시
desc.OutputWindow = m_hWnd;                         // 렌더링 결과를 출력할 윈도우 핸들
desc.Windowed = true;                               // 창 모드 여부 (true: 창 모드, false: 전체 화면 모드)
desc.SwapEffect = DXGI_SWAP_EFFECT_DISCARD;         // 버퍼 교체 후 이전 프론트 버퍼의 내용을 버림 (가장 일반적)
desc.Flags = 0;                                     // 추가 옵션 (기본값 없음)

// 멀티샘플링(MSAA) 설정 (일반적으로 기본값 사용)
desc.SampleDesc.Count = 1;   // 멀티샘플링 개수
desc.SampleDesc.Quality = 0; // 멀티샘플링 품질

desc.OutputWindow= m_hWnd;  //  출력하려는 목적지 윈도우
desc.Windowed = true;       //  창모드거나 전체화면 (true면 창,false면 전체화면)
```

* `BufferCount`: 백 버퍼의 개수입니다. 1로 설정하면 프론트 버퍼 1개, 백 버퍼 1개로 구성된 기본적인 더블 버퍼링 환경이 만들어집니다. 더 많은 값을 설정하여 트리플 버퍼링 등을 구현할 수도 있음

* `BufferDesc.Format`: 버퍼가 저장할 픽셀 데이터의 형식입니다. `DXGI_FORMAT_R8G8B8A8_UNORM`은 각 채널(Red, Green, Blue, Alpha)당 8비트를 사용하고, 0~1 사이의 값으로 정규화됨을 의미

* `BufferUsage` : 버퍼의 주된 용도를 나타냅니다. `DXGI_USAGE_RENDER_TARGET_OUTPUT`은 이 버퍼가 렌더링 결과물이 저장될 대상임을 의미

* `SwapEffect` : `Present()` 호출 후 백 버퍼의 내용 처리 방법을 지정

  * `DXGI_SWAP_EFFECT_DISCARD`: 가장 효율적인 옵션 중 하나로, 버퍼 교체 후 이전 프론트 버퍼(현재는 백 버퍼가 된)의 내용을 신경 쓰지 않고 버림
  * `DXGI_SWAP_EFFECT_SEQUENTIAL`: 버퍼 내용을 유지하며 순차적으로 사용 (플립 모델에서 주로 사용)
  * `DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL` / `DXGI_SWAP_EFFECT_FLIP_DISCARD`: 최신 DirectX에서 권장되는 플립 모델 효과입니다. 성능상 이점이 많음

<br>

# **스왑 체인 생성 과정**

* 스왑 체인을 실제로 생성하려면 몇 가지 DXGI 인터페이스를 통해야 함

1. `IDXGIDevice` 가져오기: 현재 사용 중인 Direct3D 디바이스(m_pDevice)에서 QueryInterface를 통해 IDXGIDevice 인터페이스를 얻기, 이는 DXGI 기능을 사용하기 위한 디바이스 표현

2. `IDXGIAdapter` 가져오기: IDXGIDevice에서 GetParent를 호출하여 디바이스가 실행 중인 그래픽 어댑터(실제 그래픽 카드)를 나타내는 IDXGIAdapter 인터페이스를 얻기

3. `IDXGIFactory` 가져오기: IDXGIAdapter에서 다시 GetParent를 호출하여 DXGI 객체(스왑 체인, 어댑터 등)를 생성하는 데 사용되는 IDXGIFactory 인터페이스를 얻기

4. `CreateSwapChain` 호출: IDXGIFactory 인터페이스의 CreateSwapChain 함수를 호출하여 최종적으로 스왑 체인을 생성. 이때 Direct3D 디바이스, 앞에서 설정한 DXGI_SWAP_CHAIN_DESC 구조체, 그리고 생성된 스왑 체인을 받을 포인터를 인자로 전달


```c++
// 스왑 체인 생성을 위한 DXGI 인터페이스 포인터
ComPtr<IDXGIDevice> pDXGIDevice = nullptr;
ComPtr<IDXGIAdapter> pDXGIAdaptor = nullptr;
ComPtr<IDXGIFactory> pDXGIFactory = nullptr;

// 1. Direct3D 디바이스에서 IDXGIDevice 인터페이스 얻기
m_pDevice->QueryInterface(__uuidof(IDXGIDevice), (void**)pDXGIDevice.GetAddressOf());

// 2. IDXGIDevice에서 부모인 IDXGIAdapter 인터페이스 얻기
pDXGIDevice->GetParent(__uuidof(IDXGIAdapter), (void**)pDXGIAdaptor.GetAddressOf());

// 3. IDXGIAdapter에서 부모인 IDXGIFactory 인터페이스 얻기
pDXGIAdaptor->GetParent(__uuidof(IDXGIFactory), (void**)pDXGIFactory.GetAddressOf());

// 4. IDXGIFactory를 사용하여 스왑 체인 생성
pDXGIFactory->CreateSwapChain(
    m_pDevice.Get(),      // 스왑 체인을 사용할 Direct3D 디바이스
    &desc,                // 위에서 설정한 스왑 체인 설정 구조체 주소
    m_pSwapChain.GetAddressOf() // 생성된 스왑 체인 인터페이스를 받을 포인터의 주소
);
```