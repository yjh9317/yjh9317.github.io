---
title: Multi RenderTarget
date: 2022-08-16
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

# **다중 렌더 타겟 (Multi-RenderTarget, MRT)**

* 단일 드로우 콜(Draw Call)에서 픽셀 셰이더(Pixel Shader)의 출력을 여러 개의 렌더 타겟 텍스처에 동시에 쓰는 DirectX 11의 기능
* 즉, 같은 씬을 한 번 렌더링하면서 각 렌더 타겟에는 서로 다른 종류의 픽셀 데이터를 저장할 수 있음

* 일반적으로 스왑 체인(SwapChain)에 연결된 렌더 타겟은 최종적으로 화면에 표시될 색상 정보를 담음
* 하지만 MRT를 사용하면, 스왑 체인에 연결되지 않은 추가적인 렌더 타겟들에 다양한 중간 데이터(예: 월드 공간 법선, 깊이 값, 재질 ID, 반사율 등)를 저장할 수 있음
*  이렇게 저장된 정보들은 이후 후처리(Post-processing) 단계나 다른 렌더링 기법(예: 지연 렌더링)에 활용되어 더욱 풍부하고 효율적인 그래픽 효과를 구현하는 데 사용

## **MRT의 주요 특징 및 용도**

### **동시 출력**

* 하나의 픽셀 셰이더 실행으로 최대 **8개의 렌더 타겟 텍스처(Render Target Texture)**와 **하나의 깊이/스텐실 버퍼(Depth/Stencil Buffer)**에 동시에 데이터를 쓸 수 있음 (DirectX 11 기준).

### **데이터 분리**

* 최종 색상 외에 다양한 중간 데이터를 각각의 텍스처에 분리하여 저장함으로써, 복잡한 셰이딩 계산을 여러 단계로 나누어 처리할 수 있음

### **주요 활용 기법**

#### **지연 렌더링 (Deferred Shading)**

* MRT를 사용하여 G-버퍼(Geometry Buffer)를 생성.
* G-버퍼에는 각 픽셀의 위치, 법선, 알베도, 스페큘러 강도 등의 지오메트리 및 재질 정보를 여러 렌더 타겟에 나누어 저장.
* 이후 이 G-버퍼를 사용하여 화면 공간에서 조명 계산을 수행.

#### **특수 효과용 버퍼**

* 특정 효과(예: 외곽선 감지, 모션 블러용 속도 버퍼, SSAO용 깊이/법선 버퍼)에 필요한 데이터를 별도의 렌더 타겟에 저장

### **깊이/스텐실 버퍼 공유**

* 여러 렌더 타겟을 동시에 사용할 때, 일반적으로 하나의 깊이/스텐실 버퍼를 공유.
* OMSetRenderTargets 함수 호출 시 깊이/스텐실 뷰(DSV)를 nullptr로 지정하면, 이전에 바인딩된 DSV를 계속 사용하거나, 만약 아무것도 없다면 깊이/스텐실 테스트가 비활성화된 것처럼 동작할 수 있음.


## **MRT 설정을 캡슐화하는 간단한 C++ 클래스 예시**

### **CMRT.h**

```c++
#pragma once
#include "CEntity.h" // 사용자 정의 기본 엔티티 클래스 (가정)
#include "CTexture.h" // 사용자 정의 텍스처 클래스 (Ptr<CTexture> 사용 가정)
#include <vector>
#include <wrl/client.h> // ComPtr 사용

// (Vec4, Ptr 등의 사용자 정의 타입이 있다고 가정)
// struct Vec4 { float x, y, z, w; /* ... 생성자 등 ... */ };
// template<typename T> using Ptr = Microsoft::WRL::ComPtr<T>; // 예시

class CMRT : public CEntity
{
private:
    Ptr<CTexture>           m_arrRT[D3D11_SIMULTANEOUS_RENDER_TARGET_COUNT]; // 최대 8개의 렌더 타겟 텍스처 포인터
    UINT                    m_iRTCount;                                  // 현재 그룹에 포함된 실제 렌더 타겟 개수
    Ptr<CTexture>           m_pDSTex;                                    // 깊이/스텐실 텍스처 포인터
    Vec4                    m_arrClearColor[D3D11_SIMULTANEOUS_RENDER_TARGET_COUNT]; // 각 렌더 타겟을 클리어할 색상

public:
    // 생성자 및 소멸자
    CMRT();
    ~CMRT();

    // MRT 그룹 생성
    void Create(UINT _iRTCount, Ptr<CTexture>* _arrRTTextures, Ptr<CTexture> _pDepthStencilTexture);

    // 특정 렌더 타겟의 클리어 색상 설정
    void SetClearColor(UINT _iRTIndex, const Vec4& _color);
    // 모든 렌더 타겟의 클리어 색상 일괄 설정
    void SetClearColors(UINT _iCount, const Vec4* _pColors);

    // 이 MRT 그룹을 파이프라인의 출력 병합기(OM) 단계에 설정
    void BindTargets(ID3D11DeviceContext* _pContext);

    // 이 MRT 그룹의 모든 렌더 타겟과 깊이/스텐실 버퍼를 클리어
    void ClearTargets(ID3D11DeviceContext* _pContext);

public:
    // 복제 방지 매크로 (사용자 정의)
    // CLONE_DISABLE(CMRT);
};
```


### **CMRT.cpp**

```c++

#include "CMRT.h"
// #include "CDevice.h" // 전역 디바이스 컨텍스트 접근용 (CONTEXT 매크로 등)

// (CDevice::GetInst()->CONTEXT() 와 같이 디바이스 컨텍스트를 가져온다고 가정)
// ID3D11DeviceContext* GetContext() { return CDevice::GetInst()->CONTEXT(); }


CMRT::CMRT()
    : m_iRTCount(0)
    , m_pDSTex(nullptr)
{
    // 기본 클리어 색상 초기화 (예: 검은색)
    for (int i = 0; i < D3D11_SIMULTANEOUS_RENDER_TARGET_COUNT; ++i)
    {
        m_arrClearColor[i] = Vec4(0.0f, 0.0f, 0.0f, 1.0f);
    }
}

CMRT::~CMRT()
{
}

void CMRT::Create(UINT _iRTCount, Ptr<CTexture>* _arrRTTextures, Ptr<CTexture> _pDepthStencilTexture)
{
    assert(_iRTCount <= D3D11_SIMULTANEOUS_RENDER_TARGET_COUNT); // 최대 8개 확인
    m_iRTCount = _iRTCount;

    for (UINT i = 0; i < m_iRTCount; ++i)
    {
        m_arrRT[i] = _arrRTTextures[i];
        assert(m_arrRT[i] != nullptr && m_arrRT[i]->GetRTV() != nullptr); // 렌더 타겟 뷰가 유효한지 확인
    }

    m_pDSTex = _pDepthStencilTexture;
    if (m_pDSTex)
    {
        assert(m_pDSTex->GetDSV() != nullptr); // 깊이/스텐실 뷰가 유효한지 확인
    }
}

void CMRT::SetClearColor(UINT _iRTIndex, const Vec4& _color)
{
    if (_iRTIndex < m_iRTCount)
    {
        m_arrClearColor[_iRTIndex] = _color;
    }
}

void CMRT::SetClearColors(UINT _iCount, const Vec4* _pColors)
{
    UINT count = min(_iCount, m_iRTCount);
    for (UINT i = 0; i < count; ++i)
    {
        m_arrClearColor[i] = _pColors[i];
    }
}

void CMRT::BindTargets(ID3D11DeviceContext* _pContext)
{
    ID3D11RenderTargetView* arrRTVs[D3D11_SIMULTANEOUS_RENDER_TARGET_COUNT] = { nullptr, };
    for (UINT i = 0; i < m_iRTCount; ++i)
    {
        if (m_arrRT[i]) // 유효한 텍스처인지 확인
        {
            arrRTVs[i] = m_arrRT[i]->GetRTV().Get(); // Ptr<CTexture>가 GetRTV() ComPtr<ID3D11RenderTargetView>를 반환하고, .Get()으로 원시 포인터 가져옴
        }
    }

    ID3D11DepthStencilView* pDSV = nullptr;
    if (m_pDSTex) // 이 MRT 그룹에 지정된 DSV가 있다면 사용
    {
        pDSV = m_pDSTex->GetDSV().Get();
    }
    else // 없다면, 현재 파이프라인에 바인딩된 DSV를 그대로 사용 (또는 nullptr로 설정하여 깊이/스텐실 테스트 안함)
    {
        // 사용자 코드의 로직: 현재 바인딩된 DSV를 가져와서 다시 설정
        // 이는 OMGetRenderTargets의 비용과 참조 카운팅 문제를 고려해야 함.
        // 더 간단하게는, 이 MRT에 DSV가 없다면 nullptr을 전달하여 깊이/스텐실을 사용하지 않거나,
        // 또는 이 MRT는 항상 특정 DSV와 함께 사용되어야 한다는 규칙을 정할 수 있음.
        // 여기서는 사용자 코드의 의도를 따라 현재 DSV를 유지하는 로직을 주석으로 남김.
        // Microsoft::WRL::ComPtr<ID3D11DepthStencilView> pCurrentDSV;
        // _pContext->OMGetRenderTargets(0, nullptr, pCurrentDSV.GetAddressOf());
        // pDSV = pCurrentDSV.Get(); // ComPtr이 자동으로 참조 카운트 관리
        // 주의: OMGetRenderTargets는 참조 카운트를 증가시키므로, ComPtr이 아니면 수동 Release 필요.
    }

    _pContext->OMSetRenderTargets(m_iRTCount, arrRTVs, pDSV);
}

void CMRT::ClearTargets(ID3D11DeviceContext* _pContext)
{
    // 각 렌더 타겟 클리어
    for (UINT i = 0; i < m_iRTCount; ++i)
    {
        if (m_arrRT[i] && m_arrRT[i]->GetRTV())
        {
            _pContext->ClearRenderTargetView(m_arrRT[i]->GetRTV().Get(), (float*)&m_arrClearColor[i]);
        }
    }

    // 깊이/스텐실 버퍼 클리어 (지정된 경우)
    if (m_pDSTex && m_pDSTex->GetDSV())
    {
        _pContext->ClearDepthStencilView(m_pDSTex->GetDSV().Get(), D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
    }
}
```

## **HLSL에서의 MRT 사용**

* 픽셀 셰이더에서 여러 렌더 타겟으로 출력하려면, 출력 구조체의 각 멤버에 해당하는 SV_Target[N] 시맨틱을 사용합니다.
* N은 0부터 7까지의 렌더 타겟 인덱스

```c++
struct PS_OUTPUT_MRT
{
    float4 target0 : SV_Target0; // 첫 번째 렌더 타겟으로 출력 (예: 최종 색상)
    float4 target1 : SV_Target1; // 두 번째 렌더 타겟으로 출력 (예: 월드 공간 법선)
    float4 target2 : SV_Target2; // 세 번째 렌더 타겟으로 출력 (예: 스페큘러 정보)
    // ... 최대 SV_Target7 까지 가능
};

PS_OUTPUT_MRT MyPixelShader_MRT(VS_OUTPUT input)
{
    PS_OUTPUT_MRT output;

    // ... 픽셀 셰이더 로직 ...

    // 예시: 각 타겟에 다른 정보 기록
    output.target0 = float4(finalLitColor, materialAlpha);
    output.target1 = float4(normalize(input.worldNormal), materialID); // 법선과 재질 ID
    output.target2 = float4(specularColor, roughness, metallic, 0.0f); // 스페큘러, 거칠기, 금속성

    return output;
}
```

* 이후 다른 셰이더(예: 조명 계산용 전체 화면 셰이더 또는 후처리 셰이더)는 이렇게 저장된 텍스처들을 입력(SRV)으로 받아 사용하여 최종 이미지를 생성하거나 다양한 효과를 적용