---
title: Input layout
date: 2022-06-23
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---

# **입력 레이아웃(Input Layout)**

* GPU가 정점 버퍼(Vertex Buffer)로부터 정점 데이터를 어떻게 읽어들여 해석해야 하는지를 정의하는 **"청사진" 또는 "설계도"**와 같음

* 이는 렌더링 파이프라인의 가장 첫 번째 단계 중 하나인 입력 조립기(Input Assembler, IA) 스테이지에 매우 중요한 정보를 제공

* `입력 레이아웃은 Vertex Shader가 입력으로 받을 정점 데이터의 정확한 구조와 내용을 애플리케이션 코드(C++)와 HLSL 셰이더 코드 간에 일치시키는 다리 역할`

### **정의**

* 입력 레이아웃은 개별 정점을 구성하는 각 요소(특성)의 데이터 형식, 의미, 메모리 내 위치 등을 상세하게 기술
  
### **역할**

* 정점 버퍼에 저장된 연속적인 바이트 스트림을 의미 있는 정점 데이터(예: 위치, 색상, 텍스처 좌표, 법선)로 구조화하여 Vertex Shader의 입력으로 전달될 수 있도록 함


## **입력 레이아웃의 주요 역할**

#### **어떤 데이터인지**

* 각 정점 요소가 어떤 의미를 가지는가 (예: 위치 정보인가, 색상 정보인가?). 이는 **시맨틱(Semantic)**을 통해 정의

#### **어떤 형식인지**

* 각 정점 요소가 어떤 데이터 타입과 몇 개의 성분으로 구성되는가 (예: float3인가, byte4인가?). 이는 DXGI_FORMAT으로 정의

#### **어디에 있는지**

* 여러 개의 정점 버퍼를 사용할 경우, 이 데이터가 몇 번째 정점 버퍼 슬롯에서 오는가? (InputSlot)

* 정점 데이터 구조체 내에서 이 요소가 몇 바이트 오프셋부터 시작하는가? (AlignedByteOffset)

#### **어떻게 공급되는지**

* 데이터가 각 정점마다 제공되는가, 아니면 여러 인스턴스에 걸쳐 한 번 제공되는가? 
  * (InputSlotClass, InstanceDataStepRate)

<br>

## **D3D11_INPUT_ELEMENT_DESC 구조체 상세**

```c++
typedef struct D3D11_INPUT_ELEMENT_DESC {
    LPCSTR                     SemanticName;     // 이 요소의 HLSL 시맨틱 이름
    UINT                       SemanticIndex;    // 동일 시맨틱 이름 사용 시 구분 인덱스
    DXGI_FORMAT                Format;           // 이 요소의 데이터 형식 및 성분 수
    UINT                       InputSlot;        // 이 요소를 가져올 정점 버퍼 슬롯 번호 (0-15)
    UINT                       AlignedByteOffset; // 정점 시작부터 이 요소까지의 바이트 오프셋
    D3D11_INPUT_CLASSIFICATION InputSlotClass;     // 데이터가 정점별인지 인스턴스별인지
    UINT                       InstanceDataStepRate; // 인스턴스별 데이터일 경우 몇 인스턴스마다 진행할지
} D3D11_INPUT_ELEMENT_DESC;
```

## **입력 레이아웃 객체 생성 (ID3D11Device::CreateInputLayout)**

* `D3D11_INPUT_ELEMENT_DESC` 구조체의 배열을 정의한 후, 이 배열과 컴파일된 Vertex Shader의 바이트코드 포인터를 `ID3D11Device::CreateInputLayout` 함수에 전달하여 `ID3D11InputLayout` 객체를 생성

* CreateInputLayout에 `사용되는 Vertex Shader 바이트코드는 해당 입력 레이아웃과 정확히 일치하는 입력 시그니처(시맨틱 이름, 순서, 형식 등)를 가져야 함`. 그렇지 않으면 CreateInputLayout 함수 호출이 실패

```c++
// 예시: 정점 구조체 (C++)
struct MyVertex
{
    XMFLOAT3 Pos;    // 위치
    XMFLOAT2 Tex;    // 텍스처 좌표
    XMFLOAT4 Color;  // 색상
};

// ID3D11Device* pDevice; // 이미 초기화되었다고 가정
// ID3DBlob* pVSBlob;     // 컴파일된 Vertex Shader 바이트코드를 담고 있다고 가정
// ID3D11InputLayout* pInputLayout = nullptr;

// 1. D3D11_INPUT_ELEMENT_DESC 배열 정의
D3D11_INPUT_ELEMENT_DESC layoutDesc[] =
{
    { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,  D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "TEXCOORD", 0, DXGI_FORMAT_R32G32_FLOAT,    0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 }, // Pos (12바이트) 이후
    { "COLOR",    0, DXGI_FORMAT_R32G32B32A32_FLOAT,0, 20, D3D11_INPUT_PER_VERTEX_DATA, 0 }  // Tex (8바이트) 이후, 12+8=20
    // AlignedByteOffset에 D3D11_APPEND_ALIGNED_ELEMENT를 사용할 수도 있습니다.
    // { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, D3D11_APPEND_ALIGNED_ELEMENT, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    // { "TEXCOORD", 0, DXGI_FORMAT_R32G32_FLOAT,    0, D3D11_APPEND_ALIGNED_ELEMENT, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    // { "COLOR",    0, DXGI_FORMAT_R32G32B32A32_FLOAT,0, D3D11_APPEND_ALIGNED_ELEMENT, D3D11_INPUT_PER_VERTEX_DATA, 0 }
};
UINT numElements = ARRAYSIZE(layoutDesc);

// 2. 입력 레이아웃 객체 생성
HRESULT hr = pDevice->CreateInputLayout(
    layoutDesc,                // D3D11_INPUT_ELEMENT_DESC 배열 포인터
    numElements,               // 배열의 요소 개수
    pVSBlob->GetBufferPointer(), // 컴파일된 Vertex Shader 바이트코드 포인터
    pVSBlob->GetBufferSize(),    // Vertex Shader 바이트코드 크기
    &pInputLayout              // 생성된 ID3D11InputLayout 객체를 받을 포인터의 주소
);

if (FAILED(hr))
{
    // 에러 처리
    // pVSBlob는 Release() 해야 함 (ComPtr이 아니라면)
    // pInputLayout도 에러 시 Release() 또는 ComPtr 사용 시 자동
    return;
}

// 3. 입력 레이아웃 설정 (렌더링 시)
// ID3D11DeviceContext* pImmediateContext; // 이미 초기화되었다고 가정
// pImmediateContext->IASetInputLayout(pInputLayout);

// 사용이 끝난 pInputLayout은 Release() 해줘야 합니다 (ComPtr이 아니라면).
```
