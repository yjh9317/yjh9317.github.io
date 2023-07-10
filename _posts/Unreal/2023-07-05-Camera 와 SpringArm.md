---
title: Camera와 SpringArm
date: 2023-07-05
categories: [unreal,unreal]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Camera**
===========

* 플레이어의 시점을 나타내기 위해 사용되는 것으로, CameraComponent로 만들어 사용한다.

* 카메라의 기본 설정으로는 `Projection Mode`와 `FOV`가 있다.

<br>

**Projection Mode**
------------
  * `Projection Mode`는 카메라의 타입을 나타낸다.

  *  `Perspective`는 원근 투영으로, 원근법을 살려서 물체의 거리에 따라 크기를 조절한다.
     * 카메라
  
  *  `Orthographic`은 직교 투영으로, 원근감없이 수직에서 내려보는 듯하게 투영한다.
     * 미니맵, 설계도

<br>

**FOV**
----------

* 시야각을 나타내는 용어로, Field Of View의 줄임말이다.

* `이 값의 크기는 카메라의 시야 범위를 결정한다`

  * FOV 값이 클수록 시야가 커지고,작을수록 시야가 작아진다.

**SprignArm**
==========

* Pawn에 붙이는 Camera를 위한 컴포넌트로, Camera의 위치를 조절하기 위한 컴포넌트로 생각하면 된다.

* SpringArm의 장점

  * `카메라의 위치, 길이, 회전 속도, 충돌 처리 방식, 흔들림 등을 조절할 수 있다.`

  * `만약 스프링암에 연결된 Camera와 Pawn 사이에 오브젝트가 충돌된다면 SpringArm이 충돌된 오브젝트 앞까지 이동한다.`


<br>

-----------

* SpringArm에 붙어있는 기능으로, Target Arm Length으로 SpringArm에 붙어있는 카메라와 Pawn사이의 거리를 조절할 수 있다.

  * Target Arm Length는 카메라로 확대하거나 축소할 때 사용한다.



<br>

**코드**
===========

```c++
// Character.h
UPROPERTY(VisibleAnywhere, Category = "Camera")
UCameraComponent* ViewCamera;

UPROPERTY(VisibleAnywhere, Category = "Camera")
USpringArmComponent* CameraBoom;


// Character.cpp
#include "Camera/CameraComponent.h"
#include "GameFramework/SpringArmComponent.h"

APlayerCharacter::APlayerCharacter()
{
    ...

    // CreateDefaultSubobject<Type> 함수로 Component 생성
    // 인자에 TEXT를 넣어 이름을 부여할 수 있다.
    CameraBoom = CreateDefaultSubobject<USpringArmComponent>(TEXT("CameraBoom"));

    // RootComponent는 상위 컴포넌트로, World에 있는 Actor의 Transform값을 가진다.
    // 다른 하위 컴포넌트는 RootComponent에 상대적인 위치값을 가진다.
    CameraBoom->SetupAttachment(GetRootComponent());

    // SpringArm의 끝에 있는 카메라와 Pawn의 거리
    CameraBoom->TargetArmLength = CameraDist;  

    // Pawn의 회전값을 SpringArm에도 적용시킨다.
    CameraBoom->bUsePawnControlRotation = true; 

    ViewCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("ViewCamera"));
    // 카메라는 RootComponent가 아닌 SpringArm의 위치값에 따라 상대적이여야 하므로
    // RootComponent가 아닌 SpringArm에 부착시킨다.
    ViewCamera->SetupAttachment(CameraBoom);
}
```