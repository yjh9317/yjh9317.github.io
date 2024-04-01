---
title: Property None 에러
date: 2024-03-10
categories: [Unreal Etc,Unreal Etc]
tags: [unreal etc]		# TAG는 반드시 소문자로 이루어져야함!
---

* 가끔 블루프린트에서 값을 바꾸다가 Property가 None이라는 로그가 뜨면서 에러가 발생

* 이 에러에 걸리면 해당 변수의 Detail창에 아무것도 뜨지 않음

* 이유는 UPROPERTY에서 바꾸고 진행하다가 생기는 에러

* 해결 방법은 변수명을 한 번 바꾸면 된다. 한 번 바꾸고 다시 기존의 변수명으로 바꿔도 정상 작동한다.