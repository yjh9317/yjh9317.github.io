---
title: IndexBuffer
date: 2022-06-04
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---

# **DirectX 인덱스 버퍼 (Index Buffer)**

* 3D 모델을 렌더링할 때, 특히 복잡한 모델은 수많은 삼각형으로 구성됨.
* 이때 **인덱스 버퍼(Index Buffer)**는 정점 버퍼(Vertex Buffer)와 함께 사용되어 렌더링 효율성을 크게 향상시키는 중요한 요소

<br>

# **인덱스 버퍼가 필요한 이유**

### **정점 버퍼만 사용할 경우**

* 첫 번째 삼각형: 3개의 정점 필요
* 두 번째 삼각형: 3개의 정점 필요
* 총 6개의 정점 데이터가 정점 버퍼에 저장
* 하지만 이 사각형은 실제로는 4개의 고유한 꼭짓점(정점)만 가짐. 두 삼각형이 공유하는 빗변의 양 끝점, 즉 2개의 정점 위치 데이터가 정점 버퍼에 중복으로 저장되는 것

* 이처럼 모델이 복잡해질수록 공유되는 정점은 더욱 많아지고, 이로 인해 상당한 메모리가 낭비되며, 중복된 정점 데이터에 대한 불필요한 처리(변환, 셰이딩 등)가 발생하여 성능 저하를 유발할 수 있음

### **인덱스 버퍼의 역할**

* 정점 버퍼에는 고유한 정점 데이터만 저장합니다.
* 인덱스 버퍼에는 이 고유한 정점들의 **인덱스(번호)**를 저장하여, 이 인덱스들을 특정 순서대로 참조함으로써 삼각형(또는 다른 기본 도형)을 정의
* 즉, 정점 데이터를 직접 나열하는 대신, `정점 버퍼의 몇 번째 정점을 사용하라`는 식으로 간접적으로 정점을 참조

### **장점**

* 메모리 절약: 중복된 정점 데이터를 저장하지 않으므로 메모리 사용량이 줄어듬
* 성능 향상: GPU가 처리해야 할 고유 정점의 수가 줄어들고, 정점 캐시 효율이 높아져 렌더링 성능이 향상될 수 있음

<br>

# **인덱스 버퍼 생성 및 사용 코드**

### **1.필요한 변수 선언**

```c++
// 인덱스 버퍼 객체를 가리킬 ComPtr 스마트 포인터
ComPtr<ID3D11Buffer> g_pIB;
```

### **2. 인덱스 데이터 정의**

* 정점 버퍼에 있는 정점들을 어떤 순서로 연결하여 삼각형을 만들지를 정의
* 예를 들어, 4개의 정점(V0, V1, V2, V3)으로 사각형을 만든다고 가정하고, 인덱스는 UINT (부호 없는 정수) 타입을 사용

```c++
// 사각형을 그리기 위한 인덱스 배열 (삼각형 2개, 총 6개의 인덱스)
// 정점 순서: V0-V1-V2-V3 (예: 시계방향 또는 반시계방향으로 일관되게)
// 삼각형 1: V0, V2, V3
// 삼각형 2: V0, V1, V2
// (인덱스 순서는 삼각형의 앞면/뒷면 판정에 영향을 주므로 중요합니다)
UINT arrIDx[6] = { 0, 2, 3,  // 첫 번째 삼각형 (V0, V2, V3)
                   0, 1, 2 }; // 두 번째 삼각형 (V0, V1, V2)
```

### **3. 버퍼 설정 (D3D11_BUFFER_DESC)**

* GPU에 생성할 인덱스 버퍼의 특성을 정의
* 정점 버퍼와 유사하지만, 몇 가지 설정이 다름

```c++
D3D11_BUFFER_DESC tBufferDesc = {};

// 버퍼의 총 크기 (바이트 단위)
tBufferDesc.ByteWidth = sizeof(UINT) * 6; // 인덱스 타입 크기 * 인덱스 개수

// 버퍼의 용도 및 CPU 접근 권한 설정
// D3D11_USAGE_DEFAULT: GPU가 읽고 쓰는 가장 일반적인 리소스.
//                      CPU에서는 직접적인 잦은 업데이트를 하지 않는 정적 데이터에 적합.
// 인덱스 버퍼는 보통 한 번 생성 후 잘 변경되지 않으므로 DEFAULT가 적합합니다.
tBufferDesc.Usage = D3D11_USAGE_DEFAULT;
tBufferDesc.CPUAccessFlags = 0; // CPU에서 이 버퍼에 접근할 필요가 없음 (값 수정 X)

// 바인딩 플래그: 버퍼가 파이프라인의 어느 단계에 바인딩될지 지정
// D3D11_BIND_INDEX_BUFFER: 이 버퍼가 인덱스 버퍼로 사용됨을 명시.
tBufferDesc.BindFlags = D3D11_BIND_INDEX_BUFFER;

// 기타 플래그 (일반적인 인덱스 버퍼에는 0)
tBufferDesc.MiscFlags = 0;
tBufferDesc.StructureByteStride = 0;
```

### **4. 초기 데이터 설정 (D3D11_SUBRESOURCE_DATA)**

* 버퍼 생성 시 함께 채워 넣을 인덱스 데이터를 지정

```c++
D3D11_SUBRESOURCE_DATA tSubDesc = {};
tSubDesc.pSysMem = arrIDx; // CPU 메모리에 있는 인덱스 데이터 배열의 시작 주소
```

### **5. 버퍼 생성 (CreateBuffer)**

* 설정된 정보들을 바탕으로 Direct3D 디바이스의 CreateBuffer 함수를 호출하여 GPU 메모리에 인덱스 버퍼를 생성

```c++
// DEVICE는 ID3D11Device* 타입의 디바이스 객체 포인터라고 가정
HRESULT hr = DEVICE->CreateBuffer(&tBufferDesc, &tSubDesc, g_pIB.GetAddressOf());
if (FAILED(hr))
{
    // 에러 처리
    assert(nullptr);
}
```

* 이제 g_pIB는 GPU에 생성된 인덱스 버퍼를 가리키게 됨

### **6. 인덱스 버퍼 설정 (IASetIndexBuffer)**

* 생성된 인덱스 버퍼를 렌더링 파이프라인의 입력 조립기(Input Assembler, IA) 단계에 설정하여 실제로 사용할 수 있도록 함
  * 이 함수는 ID3D11DeviceContext의 멤버

```c++
// CONTEXT는 ID3D11DeviceContext* 타입의 디바이스 컨텍스트 객체 포인터라고 가정

// 파이프라인에 인덱스 버퍼 설정
CONTEXT->IASetIndexBuffer(
    g_pIB.Get(),          // 사용할 인덱스 버퍼의 포인터
    DXGI_FORMAT_R32_UINT, // 인덱스의 데이터 포맷 (32비트 부호 없는 정수)
                          // (정점 수가 65536개 미만이면 DXGI_FORMAT_R16_UINT 사용 가능)
    0                     // 버퍼의 시작 부분에서부터의 오프셋 (일반적으로 0)
);
```

* 이제 DrawIndexed() 계열의 그리기 함수를 호출하면, 설정된 정점 버퍼와 인덱스 버퍼를 사용하여 GPU가 도형을 렌더링을 함
* 인덱스 버퍼를 사용하면 Draw() 대신 DrawIndexed()를 사용