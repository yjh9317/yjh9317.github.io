---
title: Actor && Pawn && Character
date: 2023-07-01
categories: [unreal,Basic]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# **Actor**

* `레벨에 배치할 수 있는 오브젝트`이며 어떤 의미로는 `컴포넌트라는 타입의 오브젝트들을 담는 컨테이너`라고 생각하면 된다.

* 기본적으로 이동, 회전, 스케일과 같은 3D Transform을 지원한다

* Actor 클래스의 인스턴스를 생성하기 위해서는 `SpawnActor` 템플릿을 사용

### 주요 Component

* `UActorComponent` : 기본 컴포넌트. 특정 액터와 연결되어 사용하지만 월드에는 존재하는 형태는 아니다

* `USceneComponent` : Transform을 갖고 있는 Actor Component. Scene Component는 계층형 방식으로 Attach가 가능하다

* `UPrimitiveComponent` : Mesh,Particle 같은 그래픽 표현이 있는 Scene Component, Physics나 Collision도 있다.

* `Root Component` : Root역할을 하는 Component. Actor의 위치,스케일,회전은 Root SceneComponent에서 가져온다.

<br>

# **Pawn**

* `플레이어나 AI 가 제어할 수 있는 모든 Actor의 Base 클래스`

*  플레이어나 AI 개체의 시각적인 모습 뿐만 아니라, 콜리전이나 기타 물리적 반응과 같은 측면에서 월드와의 상호작용 방식도 Pawn 이 규정한다

### Controller

* ` Pawn (폰) 또는 Character (캐릭터)처럼 Pawn에서 파생된 클래스를 빙의(possess)하여 그 동작을 제어할 수 있는, 눈에 보이지는 않는 액터`
  
* 기본적으로 컨트롤러와 폰에는 1:1 대응 관계인 즉, 컨트롤러는 단 하나의 폰만 제어한다.

* 제어중인 폰에 발생하는 다수의 이벤트에 대한 알림을 받아서 그 이벤트에 대해 반응하여, 해당 이벤트를 가로채고 폰의 기본 동작을 대체하는 동작을 구현할 수 있는 기회를 얻습니다. 

* 컨트롤러는 주어진 폰보다 먼저 틱을 시켜 인풋 처리와 폰 이동 사이의 지연시간을 최소화하는 것이 가능합니다.


# **Character**

* `걸어다닐 수 있는 능력을 지닌 특수 유형의 Pawn`

* `CharacterMovementComponent, CapsuleComponent,SkeletalMeshComponent `의 추가를 통해 월드에서 걷기, 달리기, 점프, 비행, 수영 등이 가능한 직립 플레이어를 표현하기 위해 디자인된 Pawn이다.

### SkeletalMeshComponent

* 고급 애니메이션을 위해 스켈레톤 Mesh를 사용

### CapsuleComponent

* 이동 Collision에 사용하기 위한 컴포넌트

* CharacterMovementComponent 에서 사용되는 복잡한 Geomerty 계산은 이 Capsule 모양의 컴포넌트를 통해서 계산한다.


### CharacterMovementComponent

* Avatar가 걷기, 달리기, 점프, 낙하, 수영 등으로 이동할 때 Rigid body Physics를 사용하지 않아도 된다.

* CharacterMovementComponent 에 설정할 수 있는 프로퍼티에는 낙하와 걷기의 마찰력, 공기와 물과 땅을 가로지르는 이동 속력, 부력, 중력 스케일, 캐릭터가 피직스 오브젝트에 행사할 수 있는 물리력 등에 대한 값이 포함된다.

* CharacterMovementComponent 는 애니메이션으로부터 오는 루트 모션 파라미터도 포함하며, 피직스로 사용할 수 있도록 이미 월드 스페이스에서 트랜스된다.