---
title: Event && Event Handler
date: 2024-04-25
categories: [unreal, Niagara]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Event && Event Handler

* 번개를 내리치는 Emitter가 있는데 그 번개가 내리치는 땅에 다른 Niagara를 생성하고 싶다면 `Event`를 사용해야 한다.

* `Event`와 `Event Handler`를 이용하면 Niagara가 서로 상호작용이 가능하다.

* Emitter 하나는 데이터 생성을 담당하고, 다른 Emitter는 그 데이터를 받아 반응하는 식이다.



# Event 종류

* Event 종류에는 다음과 같이 있다.

  * `Generate Location Event`
  
  * `Generate Collision Event`
  
  * `Generate Death Event`

* Event를 사용할 때 주의점은 프로퍼티에서 `CPU Sprite`만 사용이 가능하다는 것이다.

<br>

# 과정


#### 1번

* 먼저 번개가 떨어지는 Emitter의 프로퍼티를 다음과 같이 변경한다

  * 시뮬레이션 타깃은 반드시 `CPUSim`이여야 하고
  * `퍼시스턴트 ID 필요`를 true로 변경해야 함

<center><img src="./../../../assets/img/Unreal/Niagara/Event And EventHandler/Properties.png"></center>

#### 2번

* 그 다음 여기서 사용할 것은 Location이므로 `Particle Update`에서 `Generate Location Event`를 추가해준다

<center><img src="./../../../assets/img/Unreal/Niagara/Event And EventHandler/Generate Location event.png"></center>


#### 3번

* Location을 받을 Emitter에게는 Handler를 받아야 하므로 `프로퍼티의 스테이지에서 추가`해준다.

<center><img src="./../../../assets/img/Unreal/Niagara/Event And EventHandler/Event handler.png"></center>

#### 4번

* 그러면 아래에 이벤트 핸들러가 생성되는데 여기서 사용할 이벤트와 사용할 이벤트 종류에 대한 모듈을 추가해줘야 한다.

* 먼저 `이벤트 핸들러 프로퍼티`에서는 `소스`에서는 사용할 이벤트를 설정하고 그 외에는 원하는 방식대로 설정한다

<center><img src="./../../../assets/img/Unreal/Niagara/Event And EventHandler/Event Handler Properties.png"></center>

#### 5번

* 마지막으로 이번에 사용할 종류는 Location이므로 이벤트 핸들러에서 `Receive Location Event`를 추가해준다.

<center><img src="./../../../assets/img/Unreal/Niagara/Event And EventHandler/Receive Location Event.png"></center>

<br>

## 결과

* 번개가 내리치는 땅 부분에 Particle을 생성할 수 있다

<center><img src="./../../../assets/img/Unreal/Niagara/Event And EventHandler/result.png"></center>
