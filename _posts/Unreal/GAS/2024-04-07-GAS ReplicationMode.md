---
title: GAS Replication Mode
date: 2024-04-07
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Replication Mode**
=============

* Ability System Componet 안에는 Replicate와 관련된 Replication Mode가 있다.

* Mode는 총 3가지고 `EGameplayEffectReplicationMode` enum 안에 있으며, `Full, Minimal, Mixed`가 있다.

```c++
/** gameplay effects가 클라이언트에 어떻게 복사되는지 */
UENUM()
enum class EGameplayEffectReplicationMode : uint8
{
	/** Only replicate minimal gameplay effect info. 
    Note: this does not work for Owned AbilitySystemComponents (Use Mixed instead). */
	Minimal,
	/** Only replicate minimal gameplay effect info to simulated proxies 
        but full info to owners and autonomous proxies */
	Mixed,
	/** Replicate full gameplay info to all */
	Full,
};
```

## Full

* 싱글 플레이때 사용하는 게임 모드

* Gameplay Effect는 모든 클라이언트에게 복제된다

<br>

## Mixed

* 멀티플레이 게임이면서 Player Character에게 사용하는 모드

* Gameplay Effect가 자기 자신 클라이언트에게만 복제된다.

* Gameplay Cue와 Gameplay Tag는 모든 클라이언트에게 복제한다

* Mixed 모드는 OwnerActor의 Actor는 반드시 Controller여야 한다.

  * 자동적으로 Possessedby에서 설정되지만, 다를 경우 OwnerActor의 Owner를 Controller로 직접 설정해줘야 한다.


<br>

## Minimal

* 멀티플레이 게임이면서 AI 기반 캐릭터에게 사용하는 모드

* Gameplay Effect는 복제되지 않는다

* Gameplay Cue와 Gameplay Tag는 모든 클라이언트에게 복제한다


