---
title: Interface
date: 2023-09-25
categories: [unreal, Basic]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

* `인터페이스는 서로 연관없는 클래스들끼리 공통의 함수를 지원할 수 있게 해줄수 있는 클래스`이다.

  * 예를 들어 어떠한 트리거를 발동시키면 반응하는 함수를 필요할 수 있는데 클래스마다 타입이 다를 수 있어(APawn,AActor,UDataAsset 등)이러한 모든 클래스에게 같은 함수를 지원하고 싶을 때 인터페이스를 사용하면 좋다.

* 언리얼에서는 이러한 인터페이스 클래스를 따로 지원한다.

* 언리얼 에디터에서 인터페이스를 상속받는 클래스를 만든다면 다음과 같이 생성될 수 있다.

```c++
UINTERFACE(MinimalAPI)
class UTestInterface : public UInterface
{
	GENERATED_BODY()
};


class RPGPROJECT_API ITestInterface
{
	GENERATED_BODY()

}
```

* 여기서 ITestInterface에 순수 가상 함수를 넣어 인터페이스처럼 사용할 수 있다.

* 일반 함수도 함수 선언은 가능하다.

## 차이점

* 여기서 인터페이스와 일반 클래스의 차이점은 크게 두가지가 있다.

* 하나는, `UCLASS 대신 UINTERFACE를 사용한다`

* 다른 하나는,`U가 붙은 Interface 클래스는 실제 인터페이스가 아닌, 언리얼 엔진의 리플렉션 시스템을 위해 존재하는 비어있는 클래스이다
실제 인터페이스는 I가 붙은 Interface 클래스이다.`


<br>

## 인터페이스 지정자

* 인터페이스 지정자에는 다음과 같이 있다.

#### `BlueprintType` 

  * 인터페이스를 블루프린트로 노출시키는 지정자

#### `DependsOn=(ClassName1,ClassName2, ...)`

  * 나열된 ClassName1... 등은 인터페이스 앞에 컴파일이 된다

#### MinimalAPI

  * 클래스의 타입만 다른 모듈에서 사용할 수 있도록 노출시킨다.

  * 클래스는 타입변환 가능하지만, 그 클래스의 함수는 (인라인 메서드를 제외하고) 호출할 수 없다

  * 그로인해, 다른 모듈에서 접근할 수 없는 함수가 모두 필요치 않은 클래스에 대해 모든 것을 익스포트하지 않아 컴파일 시간이 빨라집니다.

<br>


**인터페이스 상속확인**
===================

* 예를 들어 어떤 인터페이스를 상속받아 그 인터페이스의 함수로 특정 행동을 구현했다면, 다른 클래스에서 특정 행동을 하는 Actor만 따로 추출해서 따로 기능을 만들고 싶을 때도 있는데 그럴때 인터페이스 상속을 확인하는 방법을 알아야 한다.

* 인터페이스를 상속하고 있는지 확인하고 싶으면 다음과 같은 코드를 작성하면 된다.

```c++
// 여기서 OtherActor은 인터페이스를 상속하고 있는지 확인하고 싶은 클래스 변수

// 방법1, ImplementsInterface를 사용 
if(OtherActor->GetClass()->ImplementsInterface(UTestInterface::StaticClass()))
{
    UE_LOG(LogTemp,Warning,TEXT("This Actor is inherited TestInterface1"));
}

// 방법2, Implements<> 템플릿을 사용
if(OtherActor->Implements<UTestInterface>())
{
    UE_LOG(LogTemp,Warning,TEXT("This Actor is inherited TestInterface2"));
}

// 방법3, Cast를 사용
if(ITestInterface* TestInterface = Cast<ITestInterface>(OtherActor))
{
    UE_LOG(LogTemp,Warning,TEXT("This Actor is inherited TestInterface3"));
}
```

<br>

* 충돌 델리게이트로 확인한 결과, 하단에 3개의 로그가 뜨는 모습

<center><img src="./../../../assets/img/Unreal/Term/Interface/IsInheritedInterface
.png" style="width: 100%; height: auto;"></center>
