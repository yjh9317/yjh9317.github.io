---
title: ApplyEffectActor
date: 2024-04-19
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Duration Policy

* `Instant` : 일회성 행동

* `Duration` : 일정 시간동안 적용

* `Infinite` : 무한

# Effect 관련 변수 설정

* EffectActor는 충돌해서 효과를 적용하기 때문에 그와 관련된 변수들을 제작

```c++
// 충돌 상태의 Enum class
UENUM(BlueprintType)
enum class EEffectApplicationPolicy : uint8
{
	ApplyOnOverlap,
	ApplyOnEndOverlap,
	DoNotApply
};

// 삭제 관련의 Enum class
UENUM(BlueprintType)
enum class EEffectRemovalPolicy : uint8
{
	RemoveOnEndOverlap,
	DoNotRemove
};

// Instant형식
UPROPERTY(EditAnywhere, BlueprintReadOnly, Category= "Applied Effects")
TSubclassOf<UGameplayEffect> InstantGameplayEffectClass;

UPROPERTY(EditAnywhere, BlueprintReadOnly, Category= "Applied Effects")
EEffectApplicationPolicy InstantEffectApplicationPolicy = 
                            EEffectApplicationPolicy::DoNotApply;
/*
    Duration,Infinite도 위 Instant 2개 변수와 똑같이 선언, 길어서 생략
*/


// 지워야 할 Effect 객체들을 저장하는 Map 자료구조
TMap<FActiveGameplayEffectHandle, UAbilitySystemComponent*> ActiveEffectHandles;
```

<br>

# Effect를 다른 액터에게 적용하는 함수

* GameplayEffect를 다른 Actor에게 적용하려면 우선 해당 Actor가 ASC를 갖고 있는지 확인해야 한다.

* 그 다음에는 GAS에서 제공하는 함수들로 적용한다

```c++
void AAuraEffectActor::ApplyEffectToTarget(AActor* TargetActor, TSubclassOf<UGameplayEffect> GameplayEffectClass)
{
	// Get ASC
	UAbilitySystemComponent* TargetASC = 
        UAbilitySystemBlueprintLibrary::GetAbilitySystemComponent(TargetActor);
	if(TargetASC == nullptr) return;

	check(GameplayEffectClass);
	
    // MakeEffectContext : Effect에 관련된 정보를 가지고 있는 Context를 생성
	FGameplayEffectContextHandle EffectContextHandle = TargetASC->MakeEffectContext();
    // AddSourceObject : Effect가 만들어지게 된 원인 객체를 설정
	EffectContextHandle.AddSourceObject(this);

    // MakeOutgoingSpec : Effect에 필요한 Spec을 만드는 함수
	const FGameplayEffectSpecHandle EffectSpecHandle = 
        TargetASC->MakeOutgoingSpec(GameplayEffectClass,ActorLevel,EffectContextHandle);

	// ApplyGameplayEffectSpecToSelf : 스스로에게 Effect를 적용하는 함수
	FActiveGameplayEffectHandle ActiveEffectHandle = 
        TargetASC->ApplyGameplayEffectSpecToSelf(*EffectSpecHandle.Data.Get());

    // Infinite Effect중 지워야 하는 객체들을 추가
	const bool bIsInfinite = EffectSpecHandle.Data.Get()->Def.Get()->DurationPolicy == EGameplayEffectDurationType::Infinite;
	if(bIsInfinite && InfiniteEffectRemovalPolicy == EEffectRemovalPolicy::RemoveOnEndOverlap)
	{
		ActiveEffectHandles.Add(ActiveEffectHandle, TargetASC);
	}
}
```

<br>

# Effect 충돌

### Overlap

* 위에서 만든 변수들로 충돌할 때 Enum class로 충돌 타입인지를 확인하고 적용한다

```c++
void AAuraEffectActor::OnOverlap(AActor* TargetActor)
{
	if(InstantEffectApplicationPolicy == EEffectApplicationPolicy::ApplyOnOverlap)
	{
		ApplyEffectToTarget(TargetActor, InstantGameplayEffectClass);
	}

	/*
        Duration,Infinite도 위 Instant와 똑같이 설정, 길어서 생략
    */
}
```

### EndOverlap

* Overlap과 마찬가지로 EndOverlap에서도 확인하고 지워야할 Effect가 있다면 찾아서 지워주는 작업을 한다.

```c++
void AAuraEffectActor::OnEndOverlap(AActor* TargetActor)
{
	if(InstantEffectApplicationPolicy == EEffectApplicationPolicy::ApplyOnEndOverlap)
	{
		ApplyEffectToTarget(TargetActor, InstantGameplayEffectClass);
	}
	/*
        Duration,Infinite도 위 Instant와 똑같이 설정, 길어서 생략
    */
	
	if(InfiniteEffectRemovalPolicy == EEffectRemovalPolicy::RemoveOnEndOverlap)
	{
		UAbilitySystemComponent* TargetASC = UAbilitySystemBlueprintLibrary::GetAbilitySystemComponent(TargetActor);
		if(!IsValid(TargetASC)) return;

		TArray<FActiveGameplayEffectHandle> HandlesToRemove;
		for (TTuple<FActiveGameplayEffectHandle, UAbilitySystemComponent*> HandlePair : ActiveEffectHandles)
		{
			if(TargetASC == HandlePair.Value)
			{
				TargetASC->RemoveActiveGameplayEffect(HandlePair.Key, 1);
				HandlesToRemove.Add(HandlePair.Key);
			}
		}

		for(auto& Handle : HandlesToRemove)
		{
			ActiveEffectHandles.FindAndRemoveChecked(Handle);
		}
	}
}
```