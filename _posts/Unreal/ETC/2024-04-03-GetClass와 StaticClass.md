---
title: GetClass와 StaticClass
date: 2024-04-03
categories: [unreal,Unreal Etc]
tags: [unreal etc]		# TAG는 반드시 소문자로 이루어져야함!
---

# GetClass

* UObjectBase의 클래스에 정의되어 있으며, 런타임에서 실제 객체의 클래스를 조회할때 사용된다.

<br>

# StaticClass

* CDO에 저장되어 있는 클래스, 즉 컴파일 시점의 클래스에 대한 정보들을 가져온다

<br>

# 차이

```c++
UPROPERTY(EditDefaultsOnly)
int Test = 1;
```

* 이러한 변수가 있을 때 에디터에서 int값을 10으로 바꾸고 이 변수에 대해 GetClass를 사용하면 런타임에서의 값인 10을 가져오고 StaticClass를 사용하면 컴파일 시점에서의 값인 1을 가져온다.