---
title: GENERATED_BODY
date: 2023-03-28
categories: [unreal, 용어]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---



* 언리얼 엔진에서 GENERATED_BODY 매크로와 .generated.h 파일은 UHT(Unreal Header Tool)가 생성하는 코드의 일부

GENERATED_BODY 매크로
===================
* GENERATED_BODY 매크로는 UHT가 생성하는 코드의 일부로, C++ 클래스의 정의를 완성하는 데 사용
* 이 매크로는 클래스 선언 내에 선언되며, 클래스의 멤버 변수, 함수, 이벤트 등을 정의.

* GENERATED_BODY 매크로는 코드를 생성하는 데 사용되는 매크로 중 하나.
* 이 매크로를 사용하면 UHT가 클래스 정의에 필요한 추가 코드를 자동으로 생성
  * 예를 들어, Reflection System에서 사용되는 코드, Blueprint System에서 사용되는 코드, Garbage Collection System에서 사용되는 코드 등을 자동으로 생성

<br>

.generated.h 파일
================

* .generated.h 파일은 UHT가 생성하는 헤더 파일
* 이 파일은 GENERATED_BODY 매크로를 사용하는 클래스의 헤더 파일 내에 #include되며, GENERATED_BODY 매크로를 통해 자동으로 생성된 코드를 포함

* UHT는 .generated.h 파일을 생성하여, 클래스 정의 파일 내에 포함
  * 이 파일은 클래스의 추가 코드, 특히 Reflection System, Blueprint System, Garbage Collection System 등에서 사용되는 코드를 포함합니다.

* UHT는 이 파일을 생성하여, 클래스 정의 파일의 크기를 줄이고, 클래스 선언 내에 필요한 코드만 포함시키므로, 개발자는 보다 간편하게 코드를 작성. 
  * 또한, 이 파일은 빌드 시간을 줄이는 데도 기여

<br>

Code
=================

```c++
#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "MyClass.generated.h"

UCLASS()
class MYPROJECT_API AMyClass : public AActor
{
    GENERATED_BODY()

public:
    // Constructor
    AMyClass();

    // Function
    UFUNCTION(BlueprintCallable, Category = "MyClass")
    void MyFunction();

    // Variable
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "MyClass")
    float MyVariable;
};
```


<br>

GENERATED_BODY와 .generated.h의 장점
===============
* GENERATED_BODY 매크로와 .generated.h 파일은 UHT가 생성하는 코드의 중요한 부분

* 이들을 사용하면, 클래스의 추가 코드를 자동으로 생성하여 Reflection System, Blueprint System, Garbage Collection System 등에서 사용 가능


* 또한, .generated.h 파일은 클래스 선언 파일에서 필요한 코드만 포함시키므로, 빌드 시간을 줄이는 데도 기여
