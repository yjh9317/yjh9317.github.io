---
title: Output Merger
date: 2022-07-10
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---

# 출력 병합기 (Output Merger - OM) 

* 출력 병합기(Output Merger, OM) 단계는 DirectX 그래픽스 렌더링 파이프라인의 거의 마지막 단계
* 이 단계는 `픽셀 셰이더(Pixel Shader)`에서 처리된 픽셀 조각(fragment)들의 최종 색상과 깊이 값을 받아, `현재 바인딩된 렌더 타겟(Render Target)` 및 `깊이/스텐실 버퍼(Depth/Stencil Buffer)`와 병합하는 역할

* OM 단계는 최종적으로 화면에 어떤 픽셀이 그려질지, 그리고 여러 픽셀 정보가 겹칠 때 어떻게 혼합될지를 결정
  * 이 단계에서 깊이 테스트, 스텐실 테스트, 그리고 색상 블렌딩이 수행


## 출력 병합기의 주요 작업 및 기능

### 깊이 테스트 (Depth Test / Z-Test):

#### 목적

* 3D 공간에서 어떤 물체가 다른 물체 뒤에 가려지는지(오클루전, Occlusion)를 판별하여, 보이지 않는 픽셀이 그려지는 것을 막음

#### 작동 방식:

* 현재 처리 중인 픽셀 조각의 깊이 값(래스터라이저에서 보간되었거나 픽셀 셰이더에서 SV_Depth로 출력된 값)과, 깊이 버퍼의 같은 위치에 이미 저장된 깊이 값을 비교

  * 비교 함수(예: LESS, LESS_EQUAL, GREATER 등)에 따라 테스트를 수행
  * 예를 들어, LESS_EQUAL로 설정된 경우, 새 픽셀의 깊이가 기존 깊이보다 작거나 같으면 테스트를 통과

* 테스트를 통과하면 새 픽셀의 색상이 렌더 타겟에 기록될 수 있으며, 설정에 따라 새 픽셀의 깊이 값으로 깊이 버퍼가 업데이트
  * 테스트를 실패하면 해당 픽셀 조각은 버려짐

#### 제어

* `ID3D11DepthStencilState` 객체와 `D3D11_DEPTH_STENCIL_DESC` 구조체의 `DepthEnable, DepthWriteMask, DepthFunc` 멤버를 통해 제어됨

### 스텐실 테스트 (Stencil Test):

#### 목적

*  픽셀 단위로 렌더링을 제한하거나 특정 효과(예: 아웃라인, 그림자 볼륨, 미러, 포탈, 특정 영역 마스킹)를 만들기 위해 사용되는 고급 기법입니다.

#### 작동 방식

* 스텐실 버퍼(일반적으로 깊이 버퍼와 함께 생성됨)는 픽셀당 보통 8비트의 정수 값을 저장
* 애플리케이션은 기준 값(Reference Value)을 설정하고, 스텐실 테스트를 통과(또는 실패)하거나 깊이 테스트를 통과(또는 실패)했을 때 스텐실 버퍼의 값을 어떻게 업데이트할지(예: 증가, 감소, 0으로 설정, 유지) 지정할 수 있음
* 비교 함수(예: EQUAL, NOT_EQUAL, ALWAYS)를 사용하여 현재 픽셀의 스텐실 값과 기준 값을 비교하여 테스트를 수행
  * 테스트를 실패하면 해당 픽셀 조각은 버려질 수 있음

#### 제어

* ID3D11DepthStencilState 객체와 D3D11_DEPTH_STENCIL_DESC 구조체의 StencilEnable 및 앞면/뒷면에 대한 StencilReadMask, StencilWriteMask, FrontFace (스텐실 연산), BackFace (스텐실 연산) 멤버들을 통해 제어됩니다.

### 블렌딩 (Blending):

#### 목적: 

* 반투명 객체 표현, 색상 혼합 효과(예: 빛 효과 추가, 색상 곱하기), 안티에일리어싱 등 다양한 시각적 효과를 위해 픽셀 셰이더에서 나온 색상(원본 색상, Source Color)과 렌더 타겟에 이미 있는 색상(대상 색상, Destination Color)을 혼합

#### 작동 방식:

* 깊이 및 스텐실 테스트를 통과한 픽셀에 대해 수행됨 (또는 이 테스트들이 비활성화된 경우).
* 혼합 공식은 일반적으로 다음과 같다: 
  * `FinalColor = (SourceColor * SourceBlendFactor) BlendOp (DestinationColor * DestinationBlendFactor)`
  * `BlendOp`: 혼합 연산 (예: ADD, SUBTRACT, MIN, MAX)
  * `SourceBlendFactor`, `DestinationBlendFactor`: 혼합 계수 (예: SRC_ALPHA, INV_SRC_ALPHA, ONE, ZERO)

#### 제어:

* `ID3D11BlendState` 객체와 `D3D11_BLEND_DESC` 구조체의 `BlendEnable, SrcBlend, DestBlend, BlendOp` 등의 멤버를 통해 각 렌더 타겟별로 개별적인 블렌딩 설정을 할 수 있음.

### 렌더 타겟 쓰기 (Render Target Writes):

* 위의 모든 테스트와 블렌딩 작업이 완료되면, 최종 계산된 픽셀 색상 값이 현재 바인딩된 하나 이상의 `렌더 타겟(ID3D11RenderTargetView)`에 기록됨

* DirectX 11은 **다중 렌더 타겟(Multiple Render Targets, MRT)**을 지원하여, 하나의 픽셀 셰이더 실행으로 동시에 여러 렌더 타겟에 다른 데이터를 출력할 수 있습니다 
  * (예: 디퓨즈 색상, 월드 노멀, 스페큘러 강도 등을 각각 다른 텍스처에 저장 - 지연 렌더링(Deferred Rendering)에 활용).

### 논리 연산 (Logic Operations - 선택 사항):

* 블렌딩 대신, 픽셀 셰이더의 출력 값과 렌더 타겟의 기존 값 사이에 비트 단위 논리 연산(AND, OR, XOR 등)을 수행할 수 있습니다. 
  * 흔히 사용되지는 않음.
* `ID3D11BlendState의 RenderTarget[i].LogicOpEnable` 및 `LogicOp` 멤버로 제어합니다.

### 순서 없는 접근 뷰 (UAV) 작업:

* 렌더 타겟과 함께 UAV가 출력 병합기 단계에 바인딩될 수 있음 
  * 주로 픽셀 셰이더에서 UAV에 쓰기 위한 고급 기법
* 이 경우, 픽셀 셰이더가 UAV에 기록한 데이터가 이 단계에서 최종적으로 처리될 수 있음


## 상태 객체 (State Objects) 를 통한 제어

* 출력 병합기 단계의 동작은 주로 다음과 같은 상태 객체들에 의해 결정

* `ID3D11DepthStencilState`: 깊이 테스트와 스텐실 테스트의 활성화 여부, 비교 함수, 쓰기 마스크, 스텐실 연산 등을 정의함. 
  * (D3D11_DEPTH_STENCIL_DESC 구조체로 생성)
* `ID3D11BlendState`: 색상 및 알파 블렌딩의 활성화 여부, 혼합 연산, 혼합 계수, 논리 연산 등을 정의함.
  *  (D3D11_BLEND_DESC 구조체로 생성)


## C++ 설정 간략

#### 1.렌더 타겟 뷰(RTV) 및 깊이/스텐실 뷰(DSV) 생성:

  * 스왑 체인의 백 버퍼로부터 RTV를 생성하거나, 별도의 텍스처로부터 RTV/DSV를 생성

#### 2.상태 객체 생성:

* `D3D11_DEPTH_STENCIL_DESC`를 채우고 `ID3D11Device::CreateDepthStencilState()`를 호출하여 `ID3D11DepthStencilState 객체`를 만듦
* `D3D11_BLEND_DESC`를 채우고 `ID3D11Device::CreateBlendState()`를 호출하여 `ID3D11BlendState 객체`를 만듭니다.

### 3.파이프라인에 상태 설정 (ID3D11DeviceContext 사용):

* `OMSetRenderTargets()` 또는 `OMSetRenderTargetsAndUnorderedAccessViews()`: 현재 사용할 RTV(들)와 DSV를 바인딩함
* `OMSetDepthStencilState()`: 생성한 깊이/스텐실 상태 객체를 설정.
  * 두 번째 인자로 스텐실 참조 값을 전달할 수 있음.
* `OMSetBlendState()`: 생성한 블렌드 상태 객체를 설정.
  * `블렌드 팩터(blend factor)`와 `샘플 마스크(sample mask)`도 인자로 전달할 수 있습니다.

## 결론

* 출력 병합기(OM) 단계는 렌더링 파이프라인의 최종 관문과 같다.

* 픽셀 셰이더에서 계산된 수많은 픽셀 후보들이 이곳에서 최종적으로 검증(깊이/스텐실 테스트)되고 혼합(블렌딩)되어, 우리가 화면에서 보는 최종 이미지를 형성합니다. 이 단계의 설정을 통해 매우 다양한 시각적 효과와 렌더링 최적화를 달성할 수 있다.