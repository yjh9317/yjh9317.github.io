---
title: Animation
date: 2023-07-08
categories: [unreal,unreal]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**애니메이션(Animation)**
==============

* Animation은 `애니메이션 블루프린트`를 이용하여 제작한다.

* 애니메이션 블루프린트를 만들 때는 애니메이션을 적용할 메쉬를 골라줘야 한다.


// picture


<br>

**애니메이션 그래프(Animation Graph)**
======

* 애니메이션 블루프린트를 만들고나면 기존 블루프린트와 다르게 `애니메이션 그래프`가 있다.

* 애니메이션 그래프 안에는 `출력 포즈(Output Pose)`가 있는데 내가 애니메이션 로직을 작성하고 나서 `출력 포즈의 Result`에 연결하면 메쉬에 해당 애니메이션 로직이 적용된다.

  * 단일 애니메이션을 출력 포즈와 연결하면 항상 그 애니메이션이 출력된다.

  * 하지만 애니메이션 로직을 짜서 키 입력에 따라 어떤 애니메이션을 실행시킬 지는 
    `상태 머신(State machine)`을 이용하여 로직을 짜야한다.


<br>

**상태 머신(State machine)과 상태(State)**
---------

* 상태 머신을 클릭하여 들어가게 되면 `Entry`라는 시작점이 있다.

* 화면에 우클릭을 누르거나 Entry를 좌클릭을 꾹 눌러 바깥에서 떼면 여러 항목이 나오는데 그 중 사용할 것이 `상태(State) 추가` 이다

* `State 추가`를 클릭하면 그 안에 애니메이션 포즈 출력이 있고 해당 상태로 진입하면 출력할 애니메이션을 연결한다.

  * 처음 Entry에서 State를 추가하면 `아무것도 입력하지 않은 상태`일 때 애니메이션을 의미한다.

  * State에서 다른 State를 추가하면 `State사이에 화살표`가 생기는데 화살표를 누르면 `해당 State으로 이동할 수 있는 조건`을 걸어서 조절할 수 있다.



**이벤트 그래프(Event Grpah)**
=======

* 기존 블루프린트와 동일하게 로직을 담당하는 그래프

