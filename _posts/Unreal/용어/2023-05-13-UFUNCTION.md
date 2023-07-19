---
title: UFUNCTION
date: 2023-05-13
categories: [unreal, 용어]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

UFUNCTION
================
* 함수와 같이 사용하는 리플렉션 매크로로, 매크로의 인자에 값을 넣어 여러가지 방면으로 사용할 수 있다.

<br>

BlueprintCallable
==========

```c++
UPROPERTY(BlueprintCallable)
void Func();
```

* 가장 기본적인 매크로로, 블루프린트에서 호출할 수 있게 해주는 매크로

* 블루프린트에서 재정의하지 못함

<br>

BlueprintPure
=================

```c++
UPROPERTY(BlueprintPure)
float GetFloatValue();

UPROPERTY(BlueprintPure)
float GetFloatValue(float _f);
```

*  getter성격의 함수를 블루프린트 그래프에 노출시킬 때 사용하는 매크로

<br>

BlueprintImplementableEvent
=================

```c++
UPROPERTY(BlueprintImplementableEvent)
void Func();
```

* 이 매크로가 사용된 해당 함수는 블루프린트에서 구현될 수 있도록 해준다

* 기본적으로 C++의 함수가 기본적으로 작동하고 이 함수를 블루프린트에서 재정의하거나 확장할 수 있도록 유연성을 제공한다

<br>


BlueprintNativeEvent
==============

```c++
UPROPERTY(BlueprintNativeEvent)
void Func();


virtual void Func_Implementation();
```

* 기본적으로 C++가 네이티브 함수지만, 블루프린트에서 재정의할 수 있게 해준다

* 만약 블루프린트에서 재정의되지 않았다면 C++ 함수가 호출된다

* C++코드는 함수 이름 끝에 _Implementation이 붙으면서 가상함수가 된다

* 블루프린트 함수에서 부모 함수를 호출하면 C++의 함수도 같이 호출된다.
  * 블루프린트 호출 -> C++ 호출


