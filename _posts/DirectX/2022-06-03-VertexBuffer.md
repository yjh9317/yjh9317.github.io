---
title: VertexBuffer
date: 2022-06-03
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---


# DirectX 정점 버퍼 (VertexBuffer)

* 3D 그래픽스에서 `정점 버퍼(Vertex Buffer)`는 3D 모델을 구성하는 기본 단위인 `정점(Vertex)`들의 정보를 저장하는 GPU 메모리 영역

## 정점

### 기본정의

* 공간상의 한 위치(좌표)를 나타냄.
* 정점들을 연결하여 선이나 면(주로 삼각형) 같은 기본 도형(Primitive)을 만들고, 이 도형들이 모여 3D 모델(메시, Mesh)의 형태를 이룸

### 물리적 특성

* 위치 값만 가지며, 크기는 개념적으로 무시

### 부가 정보

* DirectX에서 사용되는 정점은 단순히 위치(x, y, z) 정보뿐만 아니라, 다음과 같은 다양한 부가 데이터를 가질 수 있음

* `색상 (Color)`: 정점별 색상 정보 (예: Vec4(R, G, B, A))
* `텍스처 좌표 (Texture Coordinates)`: 모델 표면에 이미지를 입히기 위한 UV 좌표
* `선 벡터 (Normal Vector)`: 빛 계산을 통한 명암 표현에 사용되는 표면의 방향 정보


## 정점 좌표계: NDC (Normalized Device Coordinates)

* `Vertex Shader를 거친 정점들이 최종적으로 변환되는 2차원 좌표계`

### 범위

* 일반적으로 화면의 가로, 세로, 깊이 방향으로 -1.0에서 +1.0 사이의 값을 가짐
* 화면 중앙이 (0,0), 왼쪽 하단이 (-1,-1), 오른쪽 상단이 (1,1)인 2D 평면 좌표계
* 깊이 값 Z도 이 범위 내에 들어옴

### 역할

* 3D 공간(View Space)의 모든 좌표는 투영(Projection) 변환을 통해 이 NDC 공간으로 사영

### 장점

* 화면 해상도에 독립적인 좌표계를 사용하므로, 다양한 해상도에 쉽게 대응가능.
* 좌표 계산 과정을 단순화

<br>

# 정점 버퍼 생성 및 사용에 필요한 주요 변수


```c++
// 1. 정점 버퍼 객체
// ID3D11Buffer: GPU 메모리에 생성된 버퍼를 관리하는 COM 인터페이스.
// 정점 데이터, 인덱스 데이터, 상수 데이터 등 다양한 종류의 데이터를 저장할 수 있음
ComPtr<ID3D11Buffer> g_pVB; // Vertex Buffer를 가리킬 포인터

// 2. 셰이더 관련 객체
// ID3DBlob: 컴파일된 셰이더 코드나 에러 메시지 같은 임의의 바이너리 데이터를 담는 객체.
ComPtr<ID3DBlob> g_pErrBlob; // 셰이더 컴파일 시 에러 메시지를 저장할 Blob

// Vertex Shader 객체
ComPtr<ID3DBlob> g_pVSBlob; // 컴파일된 Vertex Shader 코드를 저장할 Blob
ComPtr<ID3D11VertexShader> g_pVS; // 실제 Vertex Shader 객체를 가리킬 포인터
```

<br>

# 정점 버퍼(Vertex Buffer) 생성 단계

### 1. 정점 데이터 정의

* 먼저 GPU로 보낼 정점 데이터를 CPU 메모리에 정의
* 예를 들어, 삼각형을 구성하는 3개의 정점 데이터는 다음과 같이 정의

```c++
// 사용자 정의 Vertex 구조체 (예시)
struct Vertex
{
    Vec3 vPos;   // 정점의 위치 (x, y, z)
    Vec4 vColor; // 정점의 색상 (R, G, B, A)
};

Vertex arrVtx[3] = {};

// 각 정점의 위치(NDC 좌표 사용 예시)와 색상 설정
arrVtx[0].vPos = Vec3(0.0f, 0.5f, 0.0f);   // 삼각형의 위쪽 꼭짓점
arrVtx[0].vColor = Vec4(1.f, 0.f, 0.f, 1.f); // 빨간색

arrVtx[1].vPos = Vec3(0.5f, -0.5f, 0.0f);  // 오른쪽 아래 꼭짓점
arrVtx[1].vColor = Vec4(0.f, 1.f, 0.f, 1.f); // 녹색

arrVtx[2].vPos = Vec3(-0.5f, -0.5f, 0.0f); // 왼쪽 아래 꼭짓점
arrVtx[2].vColor = Vec4(0.f, 0.f, 1.f, 1.f); // 파란색
```
    
### 2. 버퍼 설정 (D3D11_BUFFER_DESC)

* GPU에 생성할 버퍼의 특성을 정의

```c++
D3D11_BUFFER_DESC tBufferDesc = {};

// 버퍼의 총 크기 (바이트 단위)
tBufferDesc.ByteWidth = sizeof(Vertex) * 3; // Vertex 구조체 크기 * 정점 개수

// 버퍼의 용도 및 CPU 접근 권한 설정
// D3D11_USAGE_DYNAMIC: CPU에서 쓰고 GPU에서 읽는 동적 버퍼. CPU에서 자주 업데이트할 경우 사용.
// D3D11_CPU_ACCESS_WRITE: CPU에서 버퍼에 쓸 수 있도록 허용.
tBufferDesc.Usage = D3D11_USAGE_DYNAMIC;
tBufferDesc.CPUAccessFlags = D3D11_CPU_ACCESS_WRITE;
// (참고: 정적 버퍼의 경우 D3D11_USAGE_DEFAULT, CPUAccessFlags = 0 이 일반적)

// 바인딩 플래그: 버퍼가 파이프라인의 어느 단계에 바인딩될지 지정
// D3D11_BIND_VERTEX_BUFFER: 이 버퍼가 정점 버퍼로 사용됨을 명시.
tBufferDesc.BindFlags = D3D11_BIND_VERTEX_BUFFER;

// 기타 플래그 (일반적인 정점 버퍼에는 0)
tBufferDesc.MiscFlags = 0;
tBufferDesc.StructureByteStride = 0; // 구조화된 버퍼가 아닐 경우 0
```

### 3. 초기 데이터 설정 (D3D11_SUBRESOURCE_DATA)

* 버퍼를 생성할 때 함께 채워 넣을 초기 데이터를 지정

```c++
D3D11_SUBRESOURCE_DATA tSubDesc = {};
tSubDesc.pSysMem = arrVtx; // CPU 메모리에 있는 정점 데이터 배열의 시작 주소
// tSubDesc.SysMemPitch = 0; (텍스처가 아니므로 0)
// tSubDesc.SysMemSlicePitch = 0; (3D 텍스처가 아니므로 0)
```

### 4. 버퍼 생성 (CreateBuffer)

* Direct3D 디바이스의 CreateBuffer 함수를 호출하여 GPU 메모리에 정점 버퍼를 생성

```c++
// DEVICE는 ID3D11Device* 타입의 디바이스 객체 포인터라고 가정
HRESULT hr = DEVICE->CreateBuffer(&tBufferDesc, &tSubDesc, g_pVB.GetAddressOf());
if (FAILED(hr))
{
    // 에러 처리
    assert(nullptr);
}
```

* 이제 g_pVB는 GPU에 생성된 정점 버퍼를 가리키게 됨

<br>

# Vertex Shader 생성 단계

* 정점 버퍼의 데이터를 GPU에서 처리하여 변환(예: 월드 변환, 뷰 변환, 투영 변환)하는 역할을 하는 것이 Vertex Shader

*  `HLSL(High Level Shader Language)`로 작성된 셰이더 코드를 컴파일하고 객체로 만들어야 함

### 1. Vertex Shader 파일 컴파일 (D3DCompileFromFile)

* HLSL로 작성된 셰이더 파일(예: .fx 또는 .hlsl 파일)을 컴파일

```c++
UINT iFlag = 0;
#ifdef _DEBUG
    // 디버그 모드에서는 셰이더 디버깅 정보 포함
    iFlag = D3DCOMPILE_DEBUG | D3DCOMPILE_SKIP_OPTIMIZATION; // 최적화 건너뛰기 옵션도 추가 가능
#endif

// 셰이더 파일 경로 (예시)
wstring strShaderFilePath = CPathMgr::GetInst()->GetContentPath() + L"shader\\test.fx";

// D3DCompileFromFile 함수를 사용하여 셰이더 파일 컴파일
HRESULT hr = D3DCompileFromFile(
    strShaderFilePath.c_str(),      // 셰이더 파일 경로
    nullptr,                        // 매크로 정의 (없으면 nullptr)
    D3D_COMPILE_STANDARD_FILE_INCLUDE, // 표준 인클루드 처리기 사용
    "VS_Test",                      // 컴파일할 셰이더 함수의 이름 (HLSL 코드 내 함수명)
    "vs_5_0",                       // 셰이더 모델 버전 (Vertex Shader Model 5.0)
    iFlag,                          // 컴파일 플래그 (디버그 등)
    0,                              // 효과 컴파일 플래그 (일반 셰이더는 0)
    g_pVSBlob.GetAddressOf(),       // 컴파일된 셰이더 코드를 받을 Blob 객체의 주소
    g_pErrBlob.GetAddressOf()       // 에러 발생 시 메시지를 받을 Blob 객체의 주소
);

// 컴파일 실패 시 에러 메시지 출력
if (FAILED(hr))
{
    if (g_pErrBlob) // 에러 Blob이 유효한지 확인
    {
        MessageBoxA(nullptr, (char*)g_pErrBlob->GetBufferPointer(), "Vertex Shader Compile Failed!!", MB_OK);
    }
    assert(nullptr); // 또는 다른 방식으로 에러 처리
}
```


### 2. Vertex Shader 객체 생성 (CreateVertexShader)

* 성공적으로 컴파일된 셰이더 코드(Blob)를 이용하여 실제 Vertex Shader 객체를 생성

```c++
// DEVICE는 ID3D11Device* 타입의 디바이스 객체 포인터라고 가정
hr = DEVICE->CreateVertexShader(
    g_pVSBlob->GetBufferPointer(), // 컴파일된 셰이더 코드 데이터의 시작 주소
    g_pVSBlob->GetBufferSize(),    // 컴파일된 셰이더 코드의 크기
    nullptr,                       // 클래스 연결 정보 (일반적으로 nullptr)
    g_pVS.GetAddressOf()           // 생성된 Vertex Shader 객체를 받을 포인터의 주소
);

if (FAILED(hr))
{
    // 에러 처리
    assert(nullptr);
}
```

* 이제 g_pVS는 렌더링 파이프라인에 설정하여 사용할 수 있는 Vertex Shader 객체를 가리키게 됩니다. 이 셰이더는 g_pVB에 담긴 정점들을 입력으로 받아 처리하게 됨