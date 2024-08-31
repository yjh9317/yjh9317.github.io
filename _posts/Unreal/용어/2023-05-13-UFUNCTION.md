---
title: UFUNCTION
date: 2023-05-13
categories: [unreal, 용어]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# UFUNCTION

* 함수와 같이 사용하는 리플렉션 매크로로, 매크로의 인자에 값을 넣어 여러가지 방면으로 사용할 수 있다.

<br>

## BlueprintCallable


```c++
UFUNCTION(BlueprintCallable)
```

* 함수를 블루프린트에서 호출 가능하도록 만드는 매크로

* 블루프린트에서 재정의하지 못함

<br>

## BlueprintPure


```c++
UPROPERTY(BlueprintPure)
```

* 함수가 "순수 함수"임을 나타내며, 즉, 함수가 내부 상태를 변경하지 않고 입력값만을 기반으로 출력을 생성한다는 것을 의미

<br>

## BlueprintImplementableEvent


```c++
UPROPERTY(BlueprintImplementableEvent)
```

* C++에서 함수의 틀만 정의하고, 실제 구현은 블루프린트에서 하도록 설정

<br>


## BlueprintNativeEvent


```c++
UPROPERTY(BlueprintNativeEvent)
void Func();

virtual void Func_Implementation();
```

*  C++에서 기본 구현을 제공하지만, 블루프린트에서 해당 함수를 오버라이드
*  

## Server/Client/NetMulticast

```c++
UFUNCTION(Server)
UFUNCTION(Client)
UFUNCTION(NetMulticast)
```
* 함수가 네트워크에서 실행될 때 어느 쪽에서 실행되는지를 결정

## Reliable/Unreliable

```c++
UFUNCTION(Server, Reliable)
UFUNCTION(Server, Unreliable)
```

* 네트워크 함수 호출의 신뢰성을 설정

* Reliable은 반드시 전달되어야 하는 중요한 호출에 사용되고, Unreliable은 일부 누락이 허용되는 호출에 사용

## WithValidation

```c++
UFUNCTION(Server, Reliable, WithValidation)
```

* 네트워크 함수에서 서버로의 호출이 유효한지 확인하는 추가 검증 함수가 필요할 때 사용

* 이 옵션을 사용하면 _Validate라는 이름의 함수를 정의하여, 해당 함수가 실행되기 전에 검증 가능

## Exec

```c++
UFUNCTION(Exec)
```

* 콘솔 명령어로 함수를 실행

* 주로 디버깅이나 개발 도구에서 사용

## CallInEditor

```c++
UFUNCTION(CallInEditor)
```

* 함수가 에디터에서 호출될 수 있음

## Category

```c++
UFUNCTION(BlueprintCallable, Category = "Movement")
```

* 카테고리

## BlueprintAuthorityOnly

```c++
UFUNCTION(BlueprintCallable, BlueprintAuthorityOnly, Category = "Networking")
```

* 블루프린트에서 서버 권한이 있을 때만 호출할 수 있는 함수를 정의

* 주로 서버에서만 실행되어야 하는 함수에 사용

## BlueprintCosmetic

```c++
UFUNCTION(BlueprintCosmetic, Category = "UI")
```

* 함수가 클라이언트에서만 실행될 수 있음을 표시

* 서버에서 호출되지 않고, 클라이언트 측에서만 필요한 그래픽 처리 등의 작업에 사용


## NetReliable

```c++
UFUNCTION(NetReliable, Server, WithValidation)
```

* 네트워크 관련 함수가 항상 신뢰할 수 있는 방식으로 전송되도록 지정

* 중요한 데이터 전송이나 명령에 대해 사용되며, 패킷이 반드시 도달해야 하는 상황에 사용

## CustomThunk

```c++
UFUNCTION(CustomThunk)
```

* 함수가 스크립트에서 호출될 때, 커스텀 스텁(Thunk)을 사용하도록 지정

* 복잡한 블루프린트 기능이나 특수한 동작이 필요한 경우 사용


## SealedEvent

```c++
UFUNCTION(BlueprintCallable, SealedEvent, Category = "Security")
```

* 블루프린트에서 이 함수를 오버라이드할 수 없도록 막을 때 사용

## DisplayName

```c++
UFUNCTION(BlueprintCallable, DisplayName = "Calculate Damage")
```

* 블루프린트 에디터에서 함수의 이름을 지정

## UnsafeDuringActorConstruction

```c++
UFUNCTION(UnsafeDuringActorConstruction)
```

* 액터가 생성되는 동안 호출되는 것이 안전하지 않은 함수를 지정

* 액터가 생성 중일 때 특정 작업을 방지하고자 할 때 사용(액터 생성 중 실행되면 안 되는 함수에 사용)

## BlueprintInternalUseOnly

```c++
UFUNCTION(BlueprintInternalUseOnly)
```

* 블루프린트에서 내부적으로만 사용되도록 제한

* 외부에서 접근하지 못하도록 하여, 블루프린트 논리의 무결성을 보호

* 루프린트 내부 논리에서만 사용되도록 제한할 때 사용
