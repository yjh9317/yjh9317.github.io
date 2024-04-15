---
title: Collision
date: 2024-04-04
categories: [unreal, Basic]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# 콜리전 반응

* 총 3가지로, Block, Overlap, Ignore이 있다.

### Block

* 서로 Block인 두 액터 사이에 발생

* `Simulation Generates Hit Events`이 켜져 있어야 `Event Hit`이 발생한다

### Overlap

* Overlap으로 설정하면 마치 서로 Ignore하는것 처럼 보이지만, `Generate Overlap Events`이 켜져있으면 겹칠 때 이벤트가 발생한다.

* `Generate Overlap Events`이 꺼져있으면 Ignore과 Overlap은 똑같다.


### Ignore

* 액터 서로에게 영향을 끼치지 못한다.

<br>

# 콜리전 활성화(Enabled)

### No Collision

* 아무런 충돌 반응 없음

### Query Only

* `공간 쿼리(레이캐스트, 스윕, 오버랩)`에 사용


### Physics Only

* `피직스 시뮬레이션(리지드 바디, 컨스트레인트) 전용`

### Collision Enabled

* `공간 쿼리(레이캐스트, 스윕, 오버랩)와 시뮬레이션(리지드 바디, 컨스트레인트)에 모두 사용할 수 있음`

# 콜리전 오브젝트 타입


### 월드 스태틱(World Static)

* 움직이지 않는 액터에 사용

### 월드 다이내믹(World Dynamic)

* 애니메이션 or 코드에 영향받는 액터에 사용

### 폰(Pawn)

* 제어를 받을 수 있는 액터에 사용

### 피직스 바디(Physics Body)

* Physics Simulation으로 움직이는 액터에 사용

### 비히클(Vehicle),디스트럭션(Destructible)

* Vehicle, Destructible 등이 사용

<br>

## 커스텀 콜리전

* `편집->프로젝트 세팅->콜리전`에서 `오브젝트 채널`과 `트레이스 채널`을 추가할 수 있음

* `프리셋`에서 내가 원하는 Collision 에 대한 세팅이 가능