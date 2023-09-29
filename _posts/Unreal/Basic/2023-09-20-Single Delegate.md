---
title: Single Delegate
date: 2023-09-20
categories: [unreal, 용어]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

Delegate
================

* `Delegate는 임의 오브젝트의 멤버 함수를 다른 오브젝트에게 동적으로 바인딩(binding)을 시켜서, 다른 오브젝트에서 바인딩한 멤버 함수를 호출할 수 있도록 하는 기법`

* Delegate는 관찰자 패턴이라는 디자인 패턴에서 관찰자가 피관찰자에게 어떤 이벤트에 대한 반응으로 콜백 함수를 호출시키는 방식으로도 사용된다.

  * 이 디자인 패턴의 장점은 `관찰자와 피관찰자를 1:1관계로 만들어 준다는 것이다.`

  * 관찰자는 피관찰자의 목록을 멤버 변수로 두고 관찰자에게 신호를 주면 관찰자는 피관찰자들에게 `Broadcast(방송)`을 하여 피관찰자들에게 바인딩한 멤버 함수를 호출시킨다.


<br>

**싱글 델리게이트** 
============

```c++
// 반환 타입이 void이고 인자가 없는 함수를 바인딩
DECLARE_DELEGATE( DelegateName )    // void Function()

// 반환 타입이 void이고 인자가 n개인 함수를 바인딩	
// // void Function( <Param1>, <Param2>, ... )
DECLARE_DELEGATE_<Num>Params( DelegateName, Param1Type, Param2Type, ... )  

// 반환타입이 RetVal이고 인자가 없는 함수를 바인딩, 가장 앞에 반환 타입을 기록.
 // <RetVal> Function()
DECLARE_DELEGATE_RetVal( RetValType, DelegateName ) 
```

* \<Num>Params같은 경우, 선언할 때 순서와 사용할 때 순서는 같아야 한다.


## **싱글 델리게이트 함수**

### 1.델리게이트 실행 함수


<center><img src="./../../../assets/img/Unreal/Term/Single Delegate/Delegate Execute
.png" style="width: 80%; height: auto;"></center>


* Execute()는 바인딩된 함수를 실행, 바인딩된 함수가 없다면 에러 발생

* ExecuteIfBound()는 바인딩이 되어있는지 확인하고 true면 함수를 실행하고, 아니면 아무일도 일어나지 않음

* IsBound()는 델리게이트 변수가 바인딩이 되어있는지 확인하는 함수로 바인딩 돼 있으면 true, 아니면 false 반환


<br>



### 2.바인딩 관련 함수

<center><img src="./../../../assets/img/Unreal/Term/Single Delegate/SingleDelegateFunc.png" style="width: 100%; height: auto;"></center>

<br>

### **BindStatic**

* `BindStatic`은 전역 함수를 바인딩할 수 있다.

```c++
void TestGlobalFunc() { UE_LOG(LogTemp,Warning,TEXT("BindGlobalFunc") }

// DECLARE_DELEGATE(FTestDelegate); 
MainCharacter->TestBindStatic.BindStatic(&TestGlobalFunc);
```

### **BindUObject**

* `BindUObject`는 UObject를 상속받는 클래스의 함수를 바인딩할 수 있다.

* 만약 UObject를 상속받지 않는 (non-UObject)클래스의 함수를 사용하고 싶다면 `BindRaw` 함수를 사용해야 한다.

```c++
// DECLARE_DELEGATE(FTestDelegate); 

// UObject
void ATestActor::ClassFunc() { UE_LOG(LogTemp,Warning,TEXT("BindClassFunc") }
MainCharacter->TestBindClassFunc.BindUObject(this,&ATestActor::ClassFunc);

// non-UObject
void Myclass::MyFunc() { ... }
MainCharacter->TestBindClassFunc.BindRaw(this,&Myclass::MyFunc);
```

### **BindLambda**

* `BindLambda`는 람다 함수를 바인딩할 수 있다.

```c++
// DECLARE_DELEGATE(FTestDelegate); 
MainCharacter->TestBindLambda.BindLambda( [] () {UE_LOG(LogTemp,Warning,TEXT("BindLambda"))} );
```

<br>

**DELEGATE_RetVal**
=========

* `Delegate_RetVal`은 바인딩한 함수의 반환 타입을 저장해서 사용할 수 있다.

```c++
FVector Loc = MainCharacter->GetActorLocation();

// DECLARE_DELEGATE_RetVal(FVector, FTestRetVal);
MainCharacter->TestRetVal.BindLambda([Loc]() {return Loc;} );
```

* RetVal 타입의 반환값을 따로 받아 사용할 수 있다.

```c++
FVector Loc = TestRetVal.Execute();
UE_LOG(LogTemp,Warning,TEXT("%f %f %f"), Loc.X,Loc.Y,Loc.Z);
```

<br>

**DELEGATE_\<Num>Param**
==========

* `DELEGATE_<Num>Param`는 인자 수와 인자 타입이 동일한 함수를 바인딩할 수 있다.

* Num에 따라 인자 수가 정해지고, 작성하는 타입이 곧 인자의 타입이다
  
```c++
void ATestActor::ClassFuncOneParam(int num) { ... }

// DECLARE_DELEGATE_OneParam(FTestOneParam, int)
MainCharacter->TestOneParam.BindUObject(this,&ATestActor::ClassFuncOneParam);
```

* Execute 함수를 호출할 때 인자를 전달해서 사용할 수 있다.

```c++
if(TestOneParam.IsBound()) { TestOneParam.Execute(123456); }
```

<br>

# 결과

<center><img src="./../../../assets/img/Unreal/Term/Single Delegate/SingleDelegate.png" style="width: 70%; height: auto;"></center>


