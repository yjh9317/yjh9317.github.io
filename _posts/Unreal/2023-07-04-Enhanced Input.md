---
title: Enhanced Input
date: 2023-07-04
categories: [unreal,Basic]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Enhanced Input**
=============

* Enhanced Input은 언리얼 엔진5에서 기존의 Input 기능 대신 새로 나온 Input 기능

<br>

**시작하기**
=========

* 먼저 언리얼 엔진 버전에 따라 플러그인의 설치가 다르다.

* 만약 안되어 있다면 `편집 -> 플러그인`으로 가서 다음 사진과 같이 체크버튼을 눌러야 한다.

//
//대충 사진
//


<br>

* 그러고 나서 에디터창에서 다시 `편집 -> 프로젝트 세팅`으로 가서 다음과 같이 엔진 카테고리에서 입력으로 들어가고 Default Classes를 찾아 다음과 같이 변경해야 한다.

//
// 대충 사진
//


**핵심 컨셉**
==========

* Enhanced Input의 시스템에서 4가지 메인 컨셉이 있다.

  * 입력 액션
  * 입력 액션 컨텍스트
  * 입력 모디파이어
  * 입력 트리거


<br>

**입력 액션(Input Action)**
===========

* `입력 액션은 해당 키를 눌렀을 때 사용할 함수를 할당하기 위해 사용한다.`

* 입력 액션의 값 타입에는 `Digital(bool),Axis1D(float), Axis2D(Vector2D) ,Axis3D(Vector3D)`가 있다.

* 값 타입은 해당 입력 액션을 키를 눌러 입력했을 때 반환하는 값의 타입으로, 괄호 안에 있는 타입으로 사용된다.

  * 예를 들어, 버튼처럼 사용하려면 Digital(bool)을 사용하고 방향키(WASD)나 조이스틱같은 입력 장치를 사용할 때는 Axis2D(Vector2D)를 사용한다.

* Axis2D의 좌표값은 -1 ~ 1 사이의 실수값으로 나타나고 수평과 수직방향 값으로 사용된다. <br> X축 값은 왼쪽으로 이동하면 -1, 오른쪽으로 이동하면 1에 가까워지고 <br> Y축 값은 아래로 이동하면 -1, 위로 이동하면 1에 가까워진다.

  * 아무것도 누르지 않는다면 X축 Y축 둘 다 0으로 나타난다.


<br>

**입력 맵핑 컨텍스트(Input Mapping Context)**
=============

* `입력 맵핑 컨텍스트(Input Mapping Context)는 사용자의 입력을 입력 액션으로 매핑한다.`

* 컨텍스트는 사용자별로 추가하거나 삭제할 수 있고 우선순위를 지정할 수 있다.

* 향상된 입력 로컬 플레이어 서브시스템(Enhanced Input Local Player Subsystem)을 통해 하나 이상의 컨텍스트를 로컬 플레이어에게 적용하고 우선 순위를 지정해 동일한 입력을 사용하고자 하는 여러 액션 사이의 충돌을 처리할 수 있다.


<br>

**입력 트리거 상태(Input Trigger State)**
===========

* 입력 트리거 상태는 입력 이벤트가 발생한 순간에 대한 상태를 나타낸다.

* enum class인 ETriggerEvent에 멤버로 있으며, 입력 액션이 바인딩된 입력 트리거에 충족하면 입력 액션이 활성화된다.

```yaml
- None은 아무것도 누르지 않은 상태

1. Triggered : 아래와 같은 상태로 변할 때
(None -> Triggered, Ongoing -> Triggered, Triggered -> Triggered)

2. Started : 키를 누른 그 프레임에 발동
(None -> Ongoing, None -> Triggered)

3. Ongoing : 누르고 있던 키를 계속 누를 때
(Ongoing -> Ongoing)

4. Completed : 누르고 있던 키를 뗐을 때
(Ongoing -> None)

5. Canceled : 트리거 상태가 끝났을 때
(Triggered -> None)
```

<br>

**입력 모디파이어(Input Modifier)**
============

* 입력 액션에 추가적인 특성을 부여하여 다양한 상호작용을 가능하게 해주는 기능

* 모디파이어에서 주로 사용하는 것은 Negate과 스위즐 입력 축 값이 있다.
  * 스위즐 입력 축 값으로 입력 값을 Y축 값으로 변경할 수 있다.

  * Negate을 이용하여 해당 입력의 부정(+면 -)값을 얻을 수 있다.


// 사진


<br>


**C++에서 mapping context를 플레이어에 추가하는 코드**
===============

* C++ 코드에서 플레이어가 Enhanced Input을 사용하기 위해서는 사전 작업이 필요하다.

```c++
// 플레이어로 사용할 캐릭터의 BeginPlay
void APlayerCharacter::BeginPlay()
{
	// Call the base class  
	Super::BeginPlay();

	//Add Input Mapping Context
	if (APlayerController* PlayerController = Cast<APlayerController>(Controller))
	{
		if (UEnhancedInputLocalPlayerSubsystem* Subsystem =
		 ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>
		 								(PlayerController->GetLocalPlayer()))
		{
			Subsystem->AddMappingContext(DefaultMappingContext, 0);
		}
	}
}
```

<br>

**C++에서 입력 액션 바인딩**
==============

* UEnhancedInputComponent의 변수와 BindAction 함수를 이용하여 입력 액션을 바인딩한다.

* FInputActionValue으로 입력 값을 받아와 템플릿 함수인 Get을 이용하여 값을 변경하여 사용한다.

* 매개 변수의 순서는 `바인딩할 입력 액션, 트리거 이벤트, 바인딩할 객체, 바인딩할 함수`이다.

```c++
// 가상 함수인 SetupPlayerInputComponent을 이용한다.
void APlayerCharacter::SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent)
{
	if (UEnhancedInputComponent* EnhancedInputComponent = CastChecked<UEnhancedInputComponent>(PlayerInputComponent)) {

		// 점프
		EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Triggered,
											this, &ACharacter::Jump);
		EnhancedInputComponent->BindAction(JumpAction, ETriggerEvent::Completed,
											this,&ACharacter::StopJumping);

		//Moving
		EnhancedInputComponent->BindAction(MoveAction, ETriggerEvent::Triggered,
											this, &APlayerCharacter::Move);

		//Looking
		EnhancedInputComponent->BindAction(LookAction, ETriggerEvent::Triggered,
											this, &APlayerCharacter::Look);
	}
}
```

```c++
// AddMovementInput는 캐릭터의 이동을 조작하는 데 사용하는 함수
void APlayerCharacter::Move(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D MovementVector = Value.Get<FVector2D>();

	if (Controller != nullptr)
	{
		// find out which way is forward
		const FRotator Rotation = Controller->GetControlRotation();
		const FRotator YawRotation(0, Rotation.Yaw, 0);

		// get forward vector
		const FVector ForwardDirection = 
				FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
	
		// get right vector 
		const FVector RightDirection = 
				FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);

		// add movement 
		AddMovementInput(ForwardDirection, MovementVector.Y);
		AddMovementInput(RightDirection, MovementVector.X);
	}
}
```

```c++
// AddControllerYawInput 	함수는 캐릭터의 수평 회전(Yaw)을 조작
// AddControllerPitchInput 	함수는 캐릭터의 수직 회전(Pitch)을 조작
void APlayerCharacter::Look(const FInputActionValue& Value)
{
	// input is a Vector2D
	FVector2D LookAxisVector = Value.Get<FVector2D>();

	if (Controller != nullptr)
	{
		// add yaw and pitch input to controller
		AddControllerYawInput(LookAxisVector.X);
		AddControllerPitchInput(LookAxisVector.Y);
	}
}
```