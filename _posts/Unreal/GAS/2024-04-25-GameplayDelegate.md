---
title: GameplayDelegate
date: 2024-04-25
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Gameplay Delegate

* GameplayEffect를 다른 Actor나 Self에게 사용하기 위해선 `AbilitySystemComponent(ASC)`의 Delegate에 Bind를 해야한다

### Delegate 선언

```c++
// AbilitySystemComponent.h

/** Delegate for when an effect is applied */
	DECLARE_MULTICAST_DELEGATE_ThreeParams(
        FOnGameplayEffectAppliedDelegate,
        UAbilitySystemComponent*,
        const FGameplayEffectSpec&,
        FActiveGameplayEffectHandle);
```

* 다음과 같이 작동시키는 ASC, 발동시킬 GameplayEffect의 Sepc과 Handle을 받는 Delegate가 선언되어 있다.


### Delegate 변수

```c++
// AbilitySystemComponent.h

/** Called on server whenever a GE is applied to self. This includes instant and duration based GEs. */
FOnGameplayEffectAppliedDelegate OnGameplayEffectAppliedDelegateToSelf;

/** Called on server whenever a GE is applied to someone else. This includes instant and duration based GEs. */
FOnGameplayEffectAppliedDelegate OnGameplayEffectAppliedDelegateToTarget;

/** Called on both client and server whenever a duraton based GE is added (E.g., instant GEs do not trigger this). */
FOnGameplayEffectAppliedDelegate OnActiveGameplayEffectAddedDelegateToSelf;

/** Called on server whenever a periodic GE executes on self */
FOnGameplayEffectAppliedDelegate OnPeriodicGameplayEffectExecuteDelegateOnSelf;

/** Called on server whenever a periodic GE executes on target */
FOnGameplayEffectAppliedDelegate OnPeriodicGameplayEffectExecuteDelegateOnTarget;
```

* 위와 같이 server와 clinet, Self와 Target등에 따라 여러가지 Delegate가 선언되어 있고 목적에 맞는 Delegate에 바인딩 시켜서 브로드캐스트를 하면 된다.