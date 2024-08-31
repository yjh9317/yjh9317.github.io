---
title: Leader Emitter
date: 2024-05-12
categories: [unreal, Niagara]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Leader Particle

* 이전 장에서 Map Get에 `Particle Attribute Leader`값을 사용하면 Niagara 안의 다른 Emitter에서 사용된 값을 가져올 수 있다.


### 순서


<br>

* Follower Emitter에 Scratch을 추가하고

<center><img src="./../../../assets/img/Unreal/Niagara/Leader Emitter/Ledaer Follower.png"></center>

<br>

* 아래와 Map에 `Particle Attribute Leader`를 추가한 다음 Map Set에 설정해주면


<center><img src="./../../../assets/img/Unreal/Niagara/Leader Emitter/Leader Emitter.png"></center>

<br>

* 그 Scratch의 Emitter에서 사용하고자 하는 Niagara의 이름을 넣어주면 된다

<center><img src="./../../../assets/img/Unreal/Niagara/Leader Emitter/Set Leader Emitter.png"></center>
