---
title: Dynamic Parameter
date: 2024-04-30
categories: [unreal, Niagara]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Dynamic Parameter

* Niagara에서 Material에 직접 값을 바꾸고 싶을 때 사용

* 블루프린트는 Material의 값을 바꿀 수 있고 Niagara값도 바꿀 수 있다

* 하지만 Material에서는 블루프린트,나이아가라의 값을 바꿀 수 없다

```
정리

- Blueprint에서 수정 가능 : Niagara, Material

- Niagara에서 수정 가능 : Material

- Material에서 수정 가능 : 없음
```

<br>

# Dynamic Parameter 연결

* 먼저 Niagara에서 사용하는 Material에서 다음과 같이 `Dynamic Parameter`를 추가해야 한다


### In Material



* Dynamic Parameter 노드를 생성하면 4개의 Parameter을 저장할 수 있는데  `이름`과 `기본 값`을 설정할 수 있다.

<br>

<center><img src="./../../../assets/img/Unreal/Niagara/Dynamic Parameter/Dynamic Parameter.png"></center>

* Parameter 이름과 Default Value를 설정하고 나서 그 값들을 동적으로 받아서 사용할 수 있다.

* 여기서 `Parameter Index`는 여러 개의 Dynamic Parameter에서 구분하기 위한 값이다.

<br> 

### In Niagara

* Niagara에서는 사용하기 위해서는 모듈로 추가해줘야 한다.

<center><img src="./../../../assets/img/Unreal/Niagara/Dynamic Parameter/Niagara Dyn.png"></center>



<br>

* 추가하고 나서 Detail에 들어가서 사용하고자 하는 Dynamic Parameter의 Index를 체크표시해주고 그 값들을 설정하면 Material에서 그 값들을 불러와서 사용한다

<center><img src="./../../../assets/img/Unreal/Niagara/Dynamic Parameter/Dynamic Parameter Detail.png"></center>