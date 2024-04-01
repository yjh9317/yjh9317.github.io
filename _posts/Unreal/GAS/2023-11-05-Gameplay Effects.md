---
title: Gameplay Effects
date: 2023-11-05
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

  * Attribute의 Operator 연산 느낌?

* Modifier은 magnitude(크기)라는 값을 가지고 이 값으로 연산에 따라 Attribute값을 수정한다.


### Modifier Operator

* `Add`
  * 빼기는 음수를 더해서 사용

* `Multiply`

* `Divide`

* `Overide`
  * magnitude에 주어진 값으로 Attribute값을 설정한다.

<br>

* magnitude는 위의 4가지 연산을 가지고 Modifier Calculate

### Modifier Calculation Type

* `Scalable float`
  * Gameplay Effect level에 따라 magnitude 값을 설정
  * Gameplay Effect는 각자 Level을 따로 갖고 있다.

* `Attribute Based`
  * 다른 Attribute에 기반하여 계산하는 타입
    * ex) Strength란 값에 따라 Damage가 바뀜

* `Custom Calculation Class(MMC)`

* `Set by Caller`
  * Key,Value 형식

<br>

# Execution


* asd


# Duration Policy

* asd

### Instant