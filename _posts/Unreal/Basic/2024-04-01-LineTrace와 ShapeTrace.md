---
title: LineTrace와 ShapeTrace
date: 2024-04-01
categories: [unreal, Basic]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# LineTrace

* Start 부터 End 까지 Line을 쏴서 충돌할 오브젝트나 채널을 정해서 FHitResult 타입의 변수에 저장하는 개념

* LineTrace를 이용하면 총구 쪽의 상대방, 아이템과 상호작용 등 여러가지가 가능하다

* `GetWorld()`함수에서 실행할 수 있다. 


## 종류

* LineTrace의 종류에는 Single과 Multi로 나눠지고, Channel과 ObjectType으로 나눠진다.

### Single/Multi

* `Single은 부딪힌 첫 번째 Object를 반환한다`

* `Multi는 부딪힌 모든 Object를 반환한다`

### Channel/ObjectType

* `Channel은 CollisionChannel에서 사용하는 유형을 지정하여 해당 유형에만 LineTrace가 작동하도록 설정한다`

* `ObjectType은 특정 오브젝트들을 LineTrace하기 위해 사용한다`


```c++
// FHitResult : 결과를 저장할 변수
// FVector    : 시작 위치, 끝 위치
// ECollisionChannel : Line에 충돌할 Collision Channel을 설정

// 아래는 기본적으로 추가하지 않아도 작동
// FCollisionQueryParams : 무시할 Actor 혹은 Tag를 추가 등 할 수 있다.
// FCollisionResponseParams : 무시할 Channel을 추가할 수 있다

GetWorld()->LineTraceSingleByChannel(
    HitResult,
    Start,
    End,
    ECollisionChannel::ECC_Visibility
);
```

## 문서

* <https://dev.epicgames.com/documentation/ko-kr/unreal-engine/traces-tutorials-in-unreal-engine>

<br>

# ShapeTrace

* 도형(박스,캡슐,구)으로 부딪히는 Object가 있다면 FHitResult 타입 변수에 저장하는 개념

* `UKismetSystemLibrary` 의 static 함수로 사용할 수 있다.


