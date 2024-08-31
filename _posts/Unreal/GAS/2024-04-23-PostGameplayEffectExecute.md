---
title: PostGameplayEffectExecute
date: 2024-04-23
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

PostGameplayEffectExecute
=============

* Attribute의 기본값을 수정하기 위해 GameplayEffect가 실행되기 직전에 호출되는 함수

* 속성의 기본값을 수정할 때처럼 오직 실행중에만 호출되기 때문에, 만약 몇초동안 어떤 버프를 주는 그런 GameplayEffect가 적용될 때는 호출되지 않는다
