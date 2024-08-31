---
title: Custom Sprite Alignment
date: 2024-04-23
categories: [unreal, Niagara]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Custom Sprite Alignment

* 기본적으로 모든 Sprite는 카메라를 향한 평면을 기준으로 사용된다.

* 그 때문에 Sprite Renderer로 Niagara를 만든다면 부자연스러운 현상이 발생한다

  * ex) 땅으로 떨어지는 번개가 바닥을 치지 않고 화면을 기준으로 위에서 아래로 내려옴

* 그 상황을 바로 잡기 위해 설정을 바꿔야 하는데 방법이 2가지가 있다.

* Sprite Renderer에 있는 `Alignment`와 `Facing Mode`가 있다

* 하지만 여기서는 Alignment을 다룬다

# 과정

* 먼저 Sprite Renderer에서 `Alignment의 속성 값을 Custom Alignment로 변경`해준다

<center><img src="./../../../assets/img/Unreal/Niagara/Custom Sprite Alignment/Custom Alignment.png"></center>

<br>

* 그 이후에 `Particle Spawn`에 있는 `Sprite Facing and Alignment`를 추가한다

<center><img src="./../../../assets/img/Unreal/Niagara/Custom Sprite Alignment/Sprite Facing and Alignment.png"></center>


* `Sprite Facing`은 `Facing Mode가 Custom Facing Vector일 때 적용되는 속성`이다

* 하지만 여기서 사용할 내용은 Alignment이기 때문에 이 장과 관련이 없다

* 다음은 `Sprite Alginment`은 `Alignment가 Custom Alignment일 때 적용되는 속성`이다

* 이 설정한 벡터 값으로 Sprite가 정렬된다.