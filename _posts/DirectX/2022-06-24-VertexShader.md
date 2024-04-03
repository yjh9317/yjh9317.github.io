---
title: Vertex Shader
date: 2022-06-24
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---

# Vertext Shader

* Input Assembler에서 도형으로 조립된 후, Vertex Shader로 넘어간다.

* Vertex Shader는 모든 정점에 호출되는 함수로, GPU에서 실행되어 매우 빠르다.

* Vertex Shader안에서 여러 특수 효과를 사용하는데 `변환(transformations),조명(lighting),변위 매핑(displacement mapping)`등을 할 수 있다.


## Transformations

* 위에서 말한 특수 효과인 변환의 과정으로 다음과 같은 과정이 이뤄진다

* 순서대로 `Local -> World -> View -> Projection` 이다.

### Local Space

* 먼저 `Local Space`는 `정점들로 이뤄진 도형(객체)의 중심을 기준으로 3개의 축을 다루는 공간`이라고 생각하면 된다.

  * 객체의 정점들은 먼저 Local Space를 기준으로 정의된다.


### World Space

* Local Space에서 정의된 도형들은 하나의 행렬을 기준으로 상대적인 위치를 갖게 되는데 이 때 사용하는 행렬을 `World 행렬`이라고 한다.

* 그리고 그러한 `World 행렬을 이용하여 넘어간 공간`을 `World Space`라 한다.

* 결국 World Space는 World 행렬을 기준으로 모든 객체에게 적용하여 기준으로 하는 것이다.

### View Space

* View Space는 `카메라를 기준으로 하는 공간`이다.

* View Space 역시 View 행렬을 갖고 있으며 이 View 행렬을 정의하는 방법은 다음과 같다.

#### View 행렬 구하는 방법

* `View Space는 카메라를 기준으로 하는데, 카메라 역시 하나의 객체`이다.

* 결국 `카메라의 위치,회전을 기준으로 다른 도형들을 배치`하면 된다.

* 그래서 카메라를 기준으로 하기 위해서는 `카메라의 역행렬`이 필요하다.

* 만약 (0,0,0)이 원점인 World Space을 기준으로 x 방향으로 5로 간 카메라와 원점에 A 객체가 있다고 가정해본다.

* 그러면 카메라는 (5,0,0)라는 좌표를 가지게 되고 원점에 있는 A 객체는 카메라를 기준으로 (-5,0,0)이라는 좌표를 갖게 된다.<br>
 결국 `이동은 카메라의 위치를 기준으로 반대 방향으로 가면 된다.`

* 이와 마찬가지로 회전 역시 적용되지만, `회전은 카메라를 기준으로 공전하는 형태의 회전이 되어야 한다.`

  * 단순하게 카메라가 오른쪽으로 회전했다고 A객체를 왼쪽으로 회전하는게 아닌, 카메라가 오른쪽으로 30도 회전했다면 A 객체는 카메라가 보는 방향 기준으로 왼쪽으로 30도 회전해야 한다.

* `결국 View 행렬을 구하는 방법은 카메라 행렬의 역행렬`이라는 것이다.


### Projection Space(투영)

* Projection은 View Space에 들어온 객체들을 