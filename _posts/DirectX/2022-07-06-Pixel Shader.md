---
title: Pixel Shader
date: 2022-07-06
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---

# 픽셀 셰이더 (Pixel Shader - PS) / 프래그먼트 셰이더 (Fragment Shader)

* DirectX 렌더링 파이프라인에서 프로그래밍 가능한 핵심 셰이더 스테이지 중 하나
  *  (OpenGL과 Vulkan에서는 **프래그먼트 셰이더(Fragment Shader)**라고도 불림
* 이 단계는 래스터라이저(Rasterizer) 단계 이후, 그리고 출력 병합기(Output Merger) 단계 이전에 위치

### 주요 역할

* `래스터라이저가 생성한 각 픽셀 조각(fragment) 또는 픽셀 후보(pixel candidate)의 최종 색상(및 선택적으로 깊이 값)을 계산`

* `"픽셀"`이라는 용어는 약간 오해의 소지가 있는데, 이 단계에서 처리되는 것은 아직 최종적으로 화면의 픽셀이 되기 전의 후보들이며, 이후 깊이/스텐실 테스트 등에 의해 버려질 수도 있음

<br>

## 픽셀 셰이더의 입력 (Inputs)

* 픽셀 셰이더는 다음과 같은 주요 입력들을 받음

### 보간된 정점 속성 (Interpolated Vertex Attributes):

* 래스터라이저 단계에서 각 픽셀 위치에 맞게 보간된 값들
* 이는 이전 셰이더 스테이지(정점 셰이더, 도메인 셰이더, 또는 지오메트리 셰이더)의 출력 구조체와 일치하는 시맨틱(Semantic)을 가진 입력 구조체를 통해 전달받음
  * 예: 텍스처 좌표 (TEXCOORD0), 정점 색상 (COLOR0), 월드 공간 위치 (WORLDPOS), 법선 벡터 (NORMAL) 등.

### 시스템 생성 값 (System-Generated Values):

* 파이프라인에 의해 자동으로 생성되는 값들
  * 예: SV_Position (픽셀의 화면 공간 XY 좌표와 깊이 Z, 동차 W 값), SV_IsFrontFace (현재 픽셀이 기본 도형의 앞면에 속하는지 여부).

### 전역 변수 및 리소스 (Global Variables and Resources):

* 상수 버퍼(Constant Buffers)로부터 전달된 균일 변수(uniform variables)
* 텍스처(Textures) - 셰이더 리소스 뷰(SRV)와 샘플러 상태(SamplerState)를 통해 접근.
(고급 기법) 순서 없는 접근 뷰(UAV) - 제한적인 읽기 접근.

<br>

## 픽셀 셰이더의 주요 작업 및 기능

* 픽셀 셰이더 내에서는 각 픽셀의 최종 모습을 결정하기 위한 다양한 연산이 수행

### 텍스처 샘플링 (Texture Sampling):

보간된 텍스처 좌표와 샘플러 상태를 사용하여 텍스처로부터 색상이나 기타 데이터(예: 법선 맵, 스페큘러 맵)를 읽어옵니다.

### 조명 계산 (Lighting Calculations):

* `픽셀 단위 조명(Per-Pixel Lighting)`을 수행하여 훨씬 부드럽고 사실적인 명암 효과를 만듭니다.
* 보간된 법선 벡터, 광원의 정보(방향, 색상, 강도), 재질의 속성(반사율, 거칠기 등)을 사용하여 `퐁(Phong), 블린-퐁(Blinn-Phong), 물리 기반 렌더링(PBR) 모델` 등 다양한 조명 모델을 구현합니다.

### 색상 계산 및 혼합 (Color Calculation and Combination):

* 텍스처에서 샘플링한 색상, 조명 계산 결과, 정점에서 보간된 색상, 재질 고유색 등을 조합하여 해당 픽셀의 최종 색상을 결정

### 특수 효과 (Special Effects):

* `법선 매핑 (Normal Mapping) / 범프 매핑 (Bump Mapping)`: 표면의 미세한 요철을 표현하여 디테일을 높임
* `시차 매핑 (Parallax Mapping)`: 법선 매핑보다 더 향상된 깊이감을 제공
* `안개 (Fog)`: 거리에 따라 물체의 색상을 안개 색과 혼합
* `투명도 처리 (Transparency`): 알파 값을 계산하여 이후 출력 병합기에서 블렌딩될 수 있도록 함
* `알파 테스팅 (Alpha Testing)`: 알파 값을 기준으로 특정 임계값보다 낮은 픽셀을 폐기(discard)

### 깊이 값 수정 (Depth Value Modification - 선택 사항):
* 일반적으로 래스터라이저가 보간한 깊이 값을 사용하지만, 픽셀 셰이더에서 SV_Depth 시맨틱을 사용하여 픽셀의 깊이 값을 직접 출력할 수도 있습니다 (주의해서 사용해야 함).

### 픽셀 폐기 (Pixel Discarding):
* HLSL의 discard 명령어를 사용하여 현재 처리 중인 픽셀 조각의 모든 처리를 중단하고, 렌더 타겟에 아무것도 쓰지 않도록 함.
  * 주로 알파 테스팅이나 복잡한 모양의 클리핑에 사용

### 픽셀 셰이더의 출력 (Outputs)

* 일반적으로 float4 타입의 최종 색상 값을 하나 이상 출력

  * 단일 렌더 타겟을 사용할 경우: SV_Target 또는 SV_Target0 시맨틱에 할당.
  * 다중 렌더 타겟(Multiple Render Targets, MRT)을 사용할 경우: SV_Target0, SV_Target1, ..., SV_TargetN 시맨틱에 각각 다른 색상 값을 출력 가능.

* 선택적으로 float 타입의 깊이 값을 SV_Depth 시맨틱에 출력할 수 있음
* (DirectX 11.1 이상, 특정 조건 하) UAV가 바인딩되어 있다면, 픽셀 셰이더에서 UAV에 데이터를 쓸 수도 있음

## HLSL 예시: 간단한 텍스처 샘플링 및 출력

```c++
// 입력 리소스
Texture2D diffuseTexture : register(t0);    // 디퓨즈 텍스처 (SRV)
SamplerState linearSampler : register(s0);  // 선형 필터링 샘플러

// Vertex Shader에서 전달받는 입력 구조체
struct PS_INPUT
{
    float4 position   : SV_Position; // 화면 공간 위치 (픽셀 셰이더에서는 주로 XY 사용)
    float2 texCoord   : TEXCOORD0;   // 보간된 텍스처 좌표
    float3 normal_ws  : NORMAL;      // 보간된 월드 공간 법선 (조명 계산용)
};

// Pixel Shader 메인 함수
// SV_Target 시맨틱은 이 함수의 반환 값이 렌더 타겟 0번으로 출력됨을 의미
float4 MyPixelShader(PS_INPUT input) : SV_Target
{
    // 1. 텍스처에서 기본 색상 샘플링
    float4 baseColor = diffuseTexture.Sample(linearSampler, input.texCoord);

    // 2. 간단한 앰비언트 + 디렉셔널 라이팅 (예시)
    float3 ambientColor = float3(0.1f, 0.1f, 0.1f);
    float3 lightDirection = normalize(float3(0.5f, 1.0f, -0.5f)); // 고정된 광원 방향
    float3 lightColor = float3(1.0f, 1.0f, 1.0f); // 흰색 광원

    // 디퓨즈 반사율 계산 (램버시안 모델)
    float diffuseIntensity = saturate(dot(input.normal_ws, -lightDirection));
    float3 diffuseColor = lightColor * diffuseIntensity * baseColor.rgb;

    // 최종 색상 = 앰비언트 + 디퓨즈
    float3 finalColor = ambientColor + diffuseColor;

    return float4(finalColor, baseColor.a); // 최종 색상과 원본 텍스처의 알파 값 반환
}
```

<br>

## C++ 설정 간략

### 1단계 

* HLSL로 작성된 픽셀 셰이더 코드를 컴파일하여 `ID3DBlob` 객체(바이트코드)를 얻음.

### 2단계 

* `ID3D11Device::CreatePixelShader()` 함수를 사용하여 컴파일된 바이트코드로부터 `ID3D11PixelShader` 객체를 생성

### 3단계 

* 렌더링 시 `ID3D11DeviceContext::PSSetShader()` 함수로 현재 사용할 픽셀 셰이더를 파이프라인에 설정

### 4단계

* 필요한 리소스(상수 버퍼, SRV, 샘플러)를 `PSSetConstantBuffers()`, `PSSetShaderResources()`, `PSSetSamplers(`) 함수로 바인딩 슬롯에 연결


## 결론

* 픽셀 셰이더는 최종 이미지의 시각적 품질을 결정짓는 매우 중요한 단계로, 현대 그래픽스에서 다양한 시각 효과를 구현하는 핵심적인 역할을 담당