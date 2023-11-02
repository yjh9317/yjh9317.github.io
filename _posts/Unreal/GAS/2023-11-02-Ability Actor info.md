---
title: Ability Actor info
date: 2023-11-02
categories: [unreal, GAS]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**GAS의 Pawn과 PlayerState 차이점**
=============

* GAS를 사용하려면 Pawn 혹은 PlayerState 클래스에서 `Ability System Component(ACS)`와 `Attribute Set(AS)`을 추가해야 한다.


### Pawn 클래스에 ACS와 AS를 추가했을 경우

* 멀티플레이 게임에서는 Pawn이 죽을 경우 그 Pawn을 삭제하고 Respawn할 수 있다.

* 이 때 Pawn이 삭제된다면 ASC와 AS 또한 없어지고 데이터 또한 날라간다.

* Respawn한 Pawn은 Default값을 가진 상태의 ACS와 AS를 얻게 된다.

* 하지만, 단순하게 만들 AI 캐릭터같은 경우는 State 클래스가 필요도 없고 Logic에서 바로 Pawn 클래스에 다가가서 값을 가져올 수 있다.

### PlayerState 클래스에 ACS와 AS를 추가했을 경우

* Pawn이 죽는다 해도 Player State 클래스에 남아있기 때문에 ASC와 AS의 데이터가 날라가지 않는다.

* 또한 Respawn한 Pawn한테 PlayerState를 그대로 적용할 수 있다.


### 결론

* `플레이어 관련 Pawn이라면 Player State Class가 적절할테고, AI Enemy같은 경우는 Pawn(Character) Class에 구현하는 것이 적절할 것이다.`

<br>

**Ability Actor info**
=======

* Enemy같은 경우는 클래스에 ASC와 AS를 생성하는 것과 달리, Player는 Player State란 클래스에 따로 생성하기 때문에 ASC가 누구의 Owner인지 알 수가 없다.

* Player State 클래스를 이용하면 저장된 ASC와 AS를 다른 Pawn에 바로 적용할 수 있지만 그 때마다 Owner가 바뀌기 때문에 Owner를 정해줘야 한다.

* 그래서 ASC에는 두 종류의 변수가 있는데 하나는 `Owner Actor`이고 다른 하나는 `Avatar Actor`이다.

### Owner Actor

* `ASC를 소유하고 있는 클래스를 의미`


### Avatar Actor

* `ASC를 사용하면서 World에 있는 Actor를 의미`


### Enemy의 경우

* Enemy 클래스에 바로 ASC와 AS를 생성하기 때문에 `Owenr Actor와 Avatar Actor가 같다`.


### Player의 경우

* Player는 Player State에서 생성하기 때문에 `Owner Actor는 Player State`가 되고, `Avatar Actor는 Player State를 사용하는 캐릭터`가 된다.


<br>

## 함수

* ASC에는 Owner Actor와 Avatar Actor를 설정하는 함수를 지원한다.

```c++
/**
*	Initialized the Abilities' ActorInfo - 
    the structure that holds information about who we are acting on and who controls us.
*   OwnerActor is the actor that logically owns this component.
*	AvatarActor is what physical actor in the world we are acting on. 
    Usually a Pawn but it could be a Tower, Building, Turret, etc, may be the same as Owner
*/
virtual void InitAbilityActorInfo(AActor* InOwnerActor, AActor* InAvatarActor);
```

* 이 함수의 선언 시점은 Controller가 Pawn에 Possess한 후에 해야 한다.

* 그리고 어떤 클래스에 ASC를 선언했냐에 따라 사용하는 함수가 Server, Client 등 다르다.

<br>

### Character Class가 ASC를 갖고 있는 경우

* `Server`에 OwnerActor와 AvatarActor를 설정하려면 `Character 클래스에 있는 PossessedBy 함수`를 사용해야 한다.

  * PossessedBy 함수는 Pawn이 PlayerController에게 빙의(possess)되는 시점에 호출되는 함수
  * PossessedBy 함수는 함수에서만 실행되기 때문에 클라이언트는 적용되지 않음

<br>

* 그래서 클라이언트에 적용하기 위해서는 `Controller 클래스에 있는 AcknowledgePossession 함수`에서 함수를 호출해야 한다.

  * AcknowledgePossession 함수는 PossessedBy와 기능은 같지만, 클라이언트의 LocalPawn에서 호출된다.




### Player State가 ASC를 갖고 있는 경우

* Server의 경우 위와 마찬가지로 `PossesedBy 함수`를 사용한다

<br>

* Client는 `OnRep_PlayerState 함수`에서 실행한다.

* OnRep 함수는 PlayerState 변수의 값이 변할 때 복제되어 호출되는 함수로, 서버에서 PlayerState가 Possesed 함수를 호출하면서 값이 바뀌면 그 값이 Client에 복제되어 적용된다.



### Enemy의 경우

* Server와 Client 모두 `BeginPlay`에서 호출하면 된다.

