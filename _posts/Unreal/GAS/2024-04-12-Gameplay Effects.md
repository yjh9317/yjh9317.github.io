---
title: Gameplay Effects
date: 2024-04-12
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Gameplay Effects**
==============

* UGameplayEffect타입의 객체로, Attribute값이나 Tag를 바꿀 때 사용하는 클래스

* 데이터로만 이루어져 있고, `Modifiers` 과 `Executions`으로 Attribute 값을 바꿀 수 있다.

<br>

# Modifier

* 게임 플레이나, Attribute 값이 바뀔 때 사용되는 복잡한 계산등 다양한 종류가 있다.

* Modifier은 magnitude(크기)라는 값을 가지고 이 값으로 연산에 따라 Attribute값을 수정한다.


### Modifier Operator

* `Add`
  * 값을 더하는 연산

* `Multiply`
  * 값을 곱하는 연산

* `Divide`
  * 값을 나누는 연산

* `Overide`
  * magnitude에 주어진 값으로 Attribute값을 설정한다.

<br>

* magnitude는 위의 4가지 연산을 가지고 Modifier Calculate

### Modifier Calculation Type

* `Scalable float`
  * magnitude값 혹은 Data Table에 의한 값에 의한 설정

* `Attribute Based`
  * 다른 Attribute에 기반하여 계산하는 타입
    * ex) Strength란 값에 따라 Damage가 바뀜

* `Custom Calculation Class(MMC)`
  * 값을 위한 전용 클래스

* `Set by Caller`
  * Key,Value 형식
  * Key에는 Name이나 Gameplay Tag으로 사용할 수 있다

<br>

# Execution

* `하나 이상의 Attribute를 바꿀 수 있고 코딩하기에 따라 원하는 방식으로 설정 가능한 강력한 방법`

# Duration Policy

* `Instant`
  * 일회성 행동

* `Has Duration`
  * 제한된 시간 동안 적용

* `Infinite`
  * 무한

# Stacking

* `어떤 버프 또는 디버프(,이 경우 게임플레이 이펙트)를 가진 대상에게 다시 적용하는 것은 물론 어떤 상황을 처리하는 정책`

# Add Gameplay Tags

* 게임 플레이 태그를 이용해서 사용하는 방법

# Grant Abilities



# Gameplay Effect Spec

* Gameplay Effect의 경량화, 즉 최적화 버전
