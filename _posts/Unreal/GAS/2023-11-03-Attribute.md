---
title: Attribute
date: 2023-11-03
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Attribute Set(AS)**
=============

* Attribute를 관리하는 클래스

* 만약 생성자에서 ASC와 AS를 같이 생성했다면 자동적으로 묶어줄 수 있다.

* ASC는 여러 개의 AS를 가질 수 있지만 같은 클래스의 AS는 여러 개를 가질 순 없다.

  * ASC에서 AS를 접근할 때 애매해지기 때문

* 그래서 보통 하나의 AS에 여러 개의 Attribute를 가지는 형태를 사용한다.

<br>

**Attribute**
=========

* Attribute는 AS에 저장되는 `FGameplayAttributeData 구조체를 갖는 객체`이고, 이 구조체의 변수 타입들은 float다.

```c++
USTRUCT(BlueprintType)
struct GAMEPLAYABILITIES_API FGameplayAttributeData
{
	GENERATED_BODY()
	FGameplayAttributeData()
		: BaseValue(0.f)
		, CurrentValue(0.f)	{}

	FGameplayAttributeData(float DefaultValue)
		: BaseValue(DefaultValue)
		, CurrentValue(DefaultValue) {}

	virtual ~FGameplayAttributeData() {}

	float GetCurrentValue() const;
	virtual void SetCurrentValue(float NewValue);
	float GetBaseValue() const;
	virtual void SetBaseValue(float NewValue);

protected:
	UPROPERTY(BlueprintReadOnly, Category = "Attribute")
	float BaseValue;

	UPROPERTY(BlueprintReadOnly, Category = "Attribute")
	float CurrentValue;
};
```

* FGameplayAttributeData는 기본적으로 BaseValue와 CurrentValue가 있다.

  * `BaseValue는 Attribute의 변하지 않는 값`
  * `CurrentValue는 GameplayEffect에 의해 BaseValue값에서 수정된 값`



* Attribute는 코드로 바로 값을 바꿀 순 있지만, Gameplay Effect를 이용하는 것이 더 선호되는 방법이다.

  * Gameplay Effect이 더 선호되는 이유는 Attribute값이 바뀌는 것을 예측(`Prediction`)하기 때문


<br>

**Attribute 생성**
============

* Attribute를 생성하려면 Attribute Set 클래스에서 생성하고 Multi에 적용시키기 위해 다음과 같은 작업을 걸쳐야 한다.

  * 변수들은 `FGameplayAttributeData` 타입으로 선언

  * UPROPERTY에 `ReplicatedUsing`를 사용하고 `OnRep_(VariableName)` 함수를 선언

  * `GetLifetimeReplicatedProps` 함수에서 `DOREPLIFETIME` 선언

  * `OnRep_(VariableName)` 함수에서도 `GAMEPLAYATTRIBUTE_REPNOTIFY`를 선언해야 한다.

* `ATTRIBUTE_ACCESSORS`는 Attribute의 Get, Set, Init 함수를 쉽게 만들어주기 위한 매크로로, GAS에서 지원하고 있다.

* 관용적으로 사용하는 느낌으로 적어주면 된다.



<br>

### 헤더파일

```c++
// Attribute의 Get,Set 함수들을 쉽게 만들어주기 위한 매크로
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)


UCLASS()
class AURA_API UAuraAttributeSet : public UAttributeSet
{
	GENERATED_BODY()

public:
    UAuraAttributeSet();
	virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& 
                                            OutLifetimeProps) const override;


	UPROPERTY(BlueprintReadOnly, ReplicatedUsing= OnRep_Health) // 변수 Replicate
	FGameplayAttributeData Health;
	ATTRIBUTE_ACCESSORS(UAuraAttributeSet, Health); // Get,Set 함수 선언


	UPROPERTY(BlueprintReadOnly, ReplicatedUsing= OnRep_MaxHealth)
	FGameplayAttributeData MaxHealth;
	ATTRIBUTE_ACCESSORS(UAuraAttributeSet, MaxHealth);



	// OnRep : 변수의 값이 바뀌면 호출되는 함수
	UFUNCTION()
	void OnRep_Health(const FGameplayAttributeData& OldHealth) const;

	UFUNCTION()
	void OnRep_MaxHealth(const FGameplayAttributeData& OldMaxHealth) const;
}
```

<br>

### 소스파일

```c++
UAuraAttributeSet::UAuraAttributeSet()
{
    // ATTRIBUTE_ACCESSORS로 생성된 Init(VariableName) 함수를 통해 해당 변수의 값을 초기화
	InitHealth(100.f);
    InitMaxHealth(100.f);
}

void UAuraAttributeSet::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& 
                                                    OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	// REPNOTIFY_Always: 변수의 값이 바뀌지 않아도 항상 Replicate
	// REPNOTIFY_OnChanged : 변수의 값이 바뀌면 Replicate 
	DOREPLIFETIME_CONDITION_NOTIFY(UAuraAttributeSet, Health, COND_None, REPNOTIFY_Always);
	DOREPLIFETIME_CONDITION_NOTIFY(UAuraAttributeSet, MaxHealth, COND_None, REPNOTIFY_Always);
}

void UAuraAttributeSet::OnRep_Health(const FGameplayAttributeData& OldHealth) const
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UAuraAttributeSet, Health, OldHealth);
}

void UAuraAttributeSet::OnRep_MaxHealth(const FGameplayAttributeData& OldMaxHealth) const
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UAuraAttributeSet, MaxHealth, OldMaxHealth);
}
```