---
title: Normal Mapping
date: 2022-08-11
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

# **노멀 매핑 (Normal Mapping)**

* `상대적으로 적은 수의 폴리곤(Low-poly)으로 구성된 3D 모델 표면에 마치 많은 폴리곤(High-poly)으로 만들어진 것처럼 세밀한 요철과 질감을 표현하는 컴퓨터 그래픽 기술`

* 실제 지오메트리를 변경하지 않고, 빛의 반사를 조작하여 시각적으로 디테일을 추가

## **핵심 원리**

### **1. 노멀 맵 텍스처 (Normal Map Texture)**

* `노멀 맵은 각 텍셀(texel)이 표면의 법선 벡터(Normal Vector) 방향 정보를 담고 있는 특수한 텍스처`
* 이 법선 벡터의 X, Y, Z 성분은 주로 텍스처의 R, G, B 채널에 각각 매핑되어 저장.
  *  일반적으로 X, Y는 -1에서 +1 범위, Z는 0에서 +1 범위의 값을 0-255 범위로 변환하여 저장
* 이 법선 벡터는 대부분 `탄젠트 공간(Tangent Space)`을 기준으로 정의

### **2. 탄젠트 공간 (Tangent Space)**

* `각 정점(vertex) 또는 표면의 특정 지점에서 정의되는 국소적인 3차원 좌표계`
* 주로 세 개의 축으로 구성
  * `법선 (Normal - N)`: 표면에 수직인 방향 (원래 지오메트리의 법선).
  * `탄젠트 (Tangent - T)`: 표면에 접하면서 주로 텍스처의 U 좌표 방향과 일치하는 벡터.
  * `바이노멀 (Binormal / Bitangent - B)`: 법선(N)과 탄젠트(T)에 모두 수직인 벡터 (N과 T의 외적으로 계산 가능), 주로 텍스처의 V 좌표 방향과 일치.
* 노멀 맵에 저장된 법선 벡터는 이 탄젠트 공간을 기준으로 "위쪽"(파란색 계열, (0,0,1)에 해당)을 향하는 것이 일반적

### 3. 계산 과정

#### **정점 셰이더 (Vertex Shader)**

* 모델의 정점 데이터로부터 법선(N), 탄젠트(T), 바이노멀(B) 벡터를 가져와 월드 공간 또는 뷰 공간으로 변환.
  * 바이노멀은 N과 T로부터 계산할 수도 있음
* 이 세 벡터를 사용하여 TBN 행렬(탄젠트 공간에서 월드/뷰 공간으로 변환하는 행렬)을 만들거나, 반대로 광원 방향과 시선 방향 같은 벡터들을 탄젠트 공간으로 변환하여 픽셀 셰이더로 전달

#### **픽셀 셰이더 (Pixel Shader)**

* 노멀 맵 텍스처에서 현재 픽셀에 해당하는 탄젠트 공간 법선 벡터를 샘플링
* 샘플링된 값(보통 0~1 범위)을 원래의 벡터 범위(-1~1)로 변환
  * 예: `normal_ts = sampledColor.rgb * 2.0 - 1.0;`

* 옵션 A (탄젠트 공간 조명 계산): 월드/뷰 공간의 광원 방향과 시선 방향을 TBN 행렬의 역행렬(또는 전치행렬, TBN이 직교한다면)을 사용하여 탄젠트 공간으로 변환.
  * 그런 다음, 노멀 맵에서 읽은 탄젠트 공간 법선을 사용하여 조명 계산(난반사, 정반사 등)을 수행

* 옵션 B (월드/뷰 공간 조명 계산): 노멀 맵에서 읽은 탄젠트 공간 법선을 TBN 행렬을 사용하여 월드 공간 또는 뷰 공간으로 변환.
  * 그런 다음, 월드/뷰 공간의 광원 방향, 시선 방향, 그리고 변환된 법선을 사용하여 조명 계산을 수행

<br>

## **정점 데이터 요구 사항 Vertex_Data**

* 노멀 매핑을 사용하려면 정점 데이터에 다음 정보가 포함되어야 함
  * 위치 (Position)
  * 텍스처 좌표 (Texture Coordinates - UV)
  * 법선 (Normal)
  * 탄젠트 (Tangent)
  * (선택적) 바이노멀/바이탄젠트 (Binormal/Bitangent) 
    * 탄젠트와 법선으로부터 계산 가능

* 탄젠트와 바이노멀 벡터는 모델링 도구나 전처리 과정에서 생성

<br>

## **HLSL 구현 예시 (픽셀 셰이더 중심) Shader_Example**

```c++
// 정점 셰이더 출력 / 픽셀 셰이더 입력 구조체
struct PS_INPUT
{
    float4 positionH     : SV_Position;
    float2 texCoord      : TEXCOORD0;
    float3 normal_ws     : NORMAL0;      // 월드 공간 법선 (원래 지오메트리)
    float3 tangent_ws    : TANGENT0;     // 월드 공간 탄젠트
    // float3 binormal_ws : BINORMAL0; // 필요시 바이노멀도 전달 (또는 픽셀 셰이더에서 계산)
    float3 position_ws   : POSITION0;    // 월드 공간 위치
};

// 텍스처 및 샘플러
Texture2D normalMapTexture : register(t1); // 노멀 맵 텍스처
SamplerState linearSampler : register(s0);

// 상수 버퍼 (예: 광원 정보, 카메라 위치)
cbuffer LightParams : register(b1)
{
    float3 lightDirection_ws; // 월드 공간에서의 광원 방향 (방향성 광원 예시)
    float3 eyePosition_ws;    // 월드 공간에서의 카메라 위치
    // ... 기타 조명 파라미터 ...
};

float4 PS_NormalMapping(PS_INPUT input) : SV_Target
{
    // 1. 노멀 맵에서 탄젠트 공간 법선 샘플링 및 변환
    float3 normal_ts_from_map = normalMapTexture.Sample(linearSampler, input.texCoord).rgb;
    normal_ts_from_map = normalize(normal_ts_from_map * 2.0f - 1.0f); // [0,1] 범위를 [-1,1] 범위로

    // 2. TBN 행렬 구성 또는 필요한 벡터들을 탄젠트 공간으로 변환
    float3 N_ws = normalize(input.normal_ws);
    float3 T_ws = normalize(input.tangent_ws - dot(input.tangent_ws, N_ws) * N_ws); // Gram-Schmidt 과정으로 T를 N에 직교하게 만듦
    float3 B_ws = normalize(cross(N_ws, T_ws)); // 바이노멀 계산 (탄젠트와 법선의 외적)

    // TBN 행렬 (탄젠트 공간 -> 월드 공간 변환용)
    float3x3 TBN_ws = float3x3(T_ws, B_ws, N_ws);
    // 또는 월드 공간 -> 탄젠트 공간 변환용 TBN 행렬 (위 행렬의 전치)
    // float3x3 worldToTangentSpace = transpose(TBN_ws);


    // 옵션 A: 탄젠트 공간에서 조명 계산
    // float3 lightDir_ts = mul(normalize(lightDirection_ws), worldToTangentSpace);
    // float3 viewDir_ts  = mul(normalize(eyePosition_ws - input.position_ws), worldToTangentSpace);
    // float3 finalNormal_ts = normal_ts_from_map;
    // ... finalNormal_ts, lightDir_ts, viewDir_ts로 조명 계산 ...

    // 옵션 B: 노멀 맵의 법선을 월드 공간으로 변환하여 조명 계산 (더 일반적)
    float3 finalNormal_ws = normalize(mul(normal_ts_from_map, TBN_ws));

    // 3. 월드 공간에서 조명 계산 (예: 간단한 디퓨즈)
    float3 lightDir_ws_norm = normalize(lightDirection_ws); // 방향성 광원은 방향 자체가 주어짐
    float diffuseFactor = saturate(dot(finalNormal_ws, -lightDir_ws_norm)); // 광원을 향하는 방향으로 계산

    float3 baseColor = float3(0.8f, 0.8f, 0.8f); // 임시 기본 색상
    float3 finalColor = baseColor * diffuseFactor;

    return float4(finalColor, 1.0f);
}
```

## **장점**

* `시각적 디테일 향상`: 적은 폴리곤으로도 매우 디테일한 표면 질감을 표현할 수 있어, 모델링 및 렌더링 비용을 절감
* `성능 효율성`: 실제 지오메트리를 늘리는 것보다 훨씬 적은 연산량과 메모리를 사용

## **고려사항**

* `실루엣(Silhouette) 불변`: 노멀 매핑은 빛의 반사만을 조작하므로, 모델의 외곽선(실루엣)은 원래의 저폴리곤 메시 형태를 그대로 따름.
  * 외곽선까지 변화시키려면 `변위 매핑(Displacement Mapping)`과 같은 기법이 필요함
* `탄젠트 공간 정밀도`: 탄젠트, 바이노멀 벡터 계산 및 보간 시 정밀도 문제가 발생할 수 있으며, 이는 노멀 매핑 결과에 영향을 줄 수 있음. 
  * 예: `MikkTSpace`와 같은 표준화된 탄젠트 생성 방식 사용
* `노멀 맵 압축`: 노멀 맵은 보통 X, Y 성분만 저장하고 Z 성분은 `sqrt(1 - x*x - y*y)`로 복원하는 방식(`BC5/3Dc 압축 포맷` 등)으로 압축하여 메모리를 절약하기도 함