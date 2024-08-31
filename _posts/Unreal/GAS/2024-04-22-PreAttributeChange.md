---
title: PreAttributeChange
date: 2024-04-22
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

PreAttributeChange
================

* UAttributeSet에 있는 가상함수

* Attribute의 Current Value의 값을 바꾸기 전에 호출되는 함수

* `PreAttributeModify/PostAttribute modify`보다 LowLevel임.

* `이 함수에서 어떤 것이든 발동시킬 수도 있기 때문에 막기 위해 추가적인 Context을 제공하지는 않는다`

  * Effect발동, Duration기반 Effect, Stacking 변화, Effect제거 등등.

* 위와 같은 항목들보다는 `Health = Clamp(Health, 0, MaxHealth)` 같이 속성 값을 단순히 제한하는 등의 단순한 검증을 수행하는 함수이다.

* 나중에 `PostGameplayEffectExecute`에서 복잡한 처리를 한다.