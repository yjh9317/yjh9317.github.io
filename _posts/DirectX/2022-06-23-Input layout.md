---
title: Input layout
date: 2022-06-23
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---

# **Input Layout**

* Input Layout은 정점을 만드는데 사용하는 청사진같은 것이다.

* Input Layout은 정점의 특성들을 구성하며 각 특성은 최대 4개의 성분(RGBA)으로 이뤄져 있다.

## 하는일

* 어떤 버퍼에 어떤 정점 특성이 들어있는지 

* 그 특성들을 어떤 순서로 조립해서 정점을 완성시킬지

* 결국 정점의 특성을 결정할 때 이용하는 것이 Input Layout

## 코드

```c++
typedef
struct D3D11_INPUT_ELEMENT_DESC
{
    LPCSTR SemanticName;
    UINT SemanticIndex;
    DXGI_FORMAT Format;
    UINT InputSlot;
    UINT AlignedByteOffset;
    D3D11_INPUT_CLASSIFICATION InputSlotClass;
    UINT InstanceDataStepRate;

} D3D11_INPUT_ELEMENT_DESC
```

* `SemanticName` : 정점 특성의 이름

  * Vertex Shader에서 사용할 시멘틱 이름이다.

* `SemanticIndex` : 같은 SemanticName을 사용할 때 구분하기 위한 인덱스

* `Format` : 해당 정점의 자료 형식과 개수

  * RGBA와 알파벳 뒤에 숫자를 붙여 사용

* `AlignedByteOffset` : 이 구조체가 지정하는 버퍼에서 처음으로 쓰일 원소의 오프셋

  * R32G32라면 8, R32G32B32라면 12, R32G32B32A32라면 16 이렇게 사용
  
  * 현재 작성하는 Layout 이전에도 Layout이 있더라면 그 Layout의 크기만큼 더해줘야 한다.

  * 

* `InputSlot` : 16개의 정점 버퍼 슬롯들 중 이 성분의 자료가 담긴 버퍼가 연결된 슬롯번호
  * 인스턴싱이 아니라면 D3D11_INPUT_PER_VERTEX_DATA를 사용


<br>

# CreateLayout

* Layout을 만들었다면 이 Layout을 `ID3D11Device의 CreateLayout` 함수를 통해 제작할 수 있다.