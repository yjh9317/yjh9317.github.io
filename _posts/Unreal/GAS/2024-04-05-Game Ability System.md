---
title: Game Ability System
date: 2024-04-05
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Game Ability System**
=============

* Game Ability System은 언리얼엔진5부터 제공하는 기능으로, RPG나 MOBA(AOS장르)를 만들기에 적합한 시스템이다.

* 공식적인 내용은 <https://github.com/tranek/GASDocumentation>에서 확인할 수 있다.

<br>

**GAS 클래스 종류**
=========

### `Ability System Component (ASC)`
* GAS에서 제공하는 Component로, 가장 핵심이 되는 컴포넌트이다.

* 캐릭터를 GAS에 사용하기 위해선 반드시 사용해야 하고 GAS의 Main Component이다.


### `Attribute Set`

* 캐릭터의 특성같은 것들을 위한 클래스인 Attribute를 관리하는 클래스로, Attribute를 GAS System와 상호작용하기 위해서 사용한다.


### `Gameplay Ability`

* 캐릭터가 하는 행위, 행동들을 함수로 캡슐화하여 사용하기 위한 클래스

* Gameplay Ability는 Ability Task라는 작업 단위들을 비동기식으로 실행할 수도 있다.

### `Ability Task`

* Gameplay Ability는 1프레임에서만 실행하기 때문에 별로 유연성이 좋지 않다.

* 시간차 or 특정 시점에서의 델리게이트로 실행되는 액션을 하려면 AbilityTask를 사용해야 한다.

### `Gameplay Effect`

* Attribute의 값을 바꿀 때 사용되는 클래스

* 즉시 바꾸거나, 시간에 따라 바꾸거나, 일정 시간마다 채우는 등 여러 파라미터와 연관지어 계산할 수 있다.

### `Gameplay Cue`

* Particle System이나 Sound등을 Multi에서 다룰 수 있다.


### `Gameplay Tag`

* Gameplay Tag는 GAS가 아닌 다른곳에서도 사용할 수 있으며, 무언가 식별하기 위해 사용할 수 있다.


<br>


# **GAS 적용**


1. 먼저 Build.cs에서 모듈을 추가해줘야 하는데 `"GameplayTags", "GameplayTasks", "GameplayAbilities"`를 추가해줘야 한다.

2. 그 다음, 캐릭터에서 GAS를 사용하기 위해서는 `Ability System Component(ASC)`과 `Ability Set(AS)`, `IAbilitySystemInterface`를 추가해줘야 한다.

3. `IAbilitySystemInterface` 안의 순수 가상 함수인 `GetAbilitySystemComponent`를 정의하고 ASC와 AS를 `CreateDefaultSubobject`함수로 생성해야 한다.

<br>

```c++
class GAMEPLAYABILITIES_API IAbilitySystemInterface
{
	GENERATED_IINTERFACE_BODY()

	/** Returns the ability system component to use for this actor. 
	It may live on another actor, such as a Pawn using the PlayerState's component */
	virtual UAbilitySystemComponent* GetAbilitySystemComponent() const = 0;
};
```

```c++
// 헤더파일, Player State으로 예를 들었지만 Pawn도 똑같이 추가해주면 된다.
class AURA_API AAuraPlayerState : public APlayerState, public IAbilitySystemInterface
{
	GENERATED_BODY()

public:
	AAuraPlayerState();
	virtual UAbilitySystemComponent* GetAbilitySystemComponent() const;
protected:
	UPROPERTY()
	TObjectPtr<UAbilitySystemComponent> AbilitySystemComponent;

	UPROPERTY()
	TObjectPtr<UAttributeSet> AttributeSet;
};
```

```c++
// 소스코드
AAuraPlayerState::AAuraPlayerState()
{
	AbilitySystemComponent = CreateDefaultSubobject<UAuraAbilitySystemComponent>
                            (TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);

	AttributeSet = CreateDefaultSubobject<UAuraAttributeSet>(TEXT("AttributeSet"));
	
	NetUpdateFrequency = 100.f;
}

UAbilitySystemComponent* AAuraPlayerState::GetAbilitySystemComponent() const
{
	return AbilitySystemComponent;
}
```   

