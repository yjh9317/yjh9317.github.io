---
title: Execution Calculation
date: 2024-05-05
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# UGameplayEffectExecutionCalculation

### 특징

* ModMagnitudeCalculate은 하나의 Attribute만 바꾸는 것이 가능했지만 Execution Calculation은 하나 이상의 Attribute를 바꾸는 것이 가능하다

* 프로그래밍 로직을 짜서 프로그래머가 원하는대로 적용이 가능해 유연성이 좋다

### 특징2

* Preciton을 지원하지 않는다

* Instant와 Perodic Effect만 사용이 가능하다

* `PreAttributeChange`에서 캡쳐가 불가하다 

* `Net Execution Policy`가 `Local Predicted, Server Initiated, Server Only`이여도 `서버에서만 실행된다`

## SnapShot

### Source

* SnapShot Capture를 사용하면 GameplayEffectSpec이 생성될 때 Attribute Value를 캡쳐한다.

* SnapShot Capture를 사용하지 않으면 GameplayEffect가 적용될 때 Attribute Value를 캡쳐한다

### Target

* Target은 Snapshot을 사용하든 안하든 GameplayEffect가 적용될 때 Attribute Value를 캡쳐한다

<br>

# 코드

```c++
UCLASS()
class AURA_API UExecCalc_Damage : public UGameplayEffectExecutionCalculation
{
	GENERATED_BODY()
public:
	UExecCalc_Damage();

	virtual void Execute_Implementation(
        const FGameplayEffectCustomExecutionParameters& ExecutionParams, 
        FGameplayEffectCustomExecutionOutput& OutExecutionOutput) const override;	
};
```

* `UGameplayEffectExecutionCalculation`를 상속받는 클래스를 만들고 <br>
  GameplayEffect가 적용될 때 실행되는 함수 `Execute` 함수를 상속받아서 사용하면 된다.

* `ExecutionParams`는 커스텀 연산할 때 사용할 파라미터가 저장되어있는 변수이고 <br> `OutExecutionOutput`는 연산하고 나온 값을 저장하기 위한 참조 변수이다
