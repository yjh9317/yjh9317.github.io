---
title: GENERATED_BODY
date: 2023-03-28
categories: [unreal,unreal]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---



언리얼 엔진에서 GENERATED_BODY 매크로와 .generated.h 파일은 UHT(Unreal Header Tool)가 생성하는 코드의 일부입니다. 이들은 언리얼 엔진의 코드 생성 시스템에서 중요한 역할을 합니다.

GENERATED_BODY 매크로
===================
* GENERATED_BODY 매크로는 UHT가 생성하는 코드의 일부로, C++ 클래스의 정의를 완성하는 데 사용됩니다.
* 이 매크로는 클래스 선언 내에 선언되며, 클래스의 멤버 변수, 함수, 이벤트 등을 정의합니다.

* GENERATED_BODY 매크로는 코드를 생성하는 데 사용되는 매크로 중 하나입니다.
* 이 매크로를 사용하면 UHT가 클래스 정의에 필요한 추가 코드를 자동으로 생성할 수 있습니다. 
  * 예를 들어, Reflection System에서 사용되는 코드, Blueprint System에서 사용되는 코드, Garbage Collection System에서 사용되는 코드 등을 자동으로 생성할 수 있습니다.

<br>

.generated.h 파일
================

* .generated.h 파일은 UHT가 생성하는 헤더 파일입니다.
* 이 파일은 GENERATED_BODY 매크로를 사용하는 클래스의 헤더 파일 내에 #include되며, GENERATED_BODY 매크로를 통해 자동으로 생성된 코드를 포함합니다.

* UHT는 .generated.h 파일을 생성하여, 클래스 정의 파일 내에 포함시킵니다.
  * 이 파일은 클래스의 추가 코드, 특히 Reflection System, Blueprint System, Garbage Collection System 등에서 사용되는 코드를 포함합니다.

* UHT는 이 파일을 생성하여, 클래스 정의 파일의 크기를 줄이고, 클래스 선언 내에 필요한 코드만 포함시키므로,
* 개발자는 보다 간편하게 코드를 작성할 수 있습니다. 또한, 이 파일은 빌드 시간을 줄이는 데도 기여합니다.

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

// MyClass.generated.h
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


* 위 코드에서 MyClass.h 파일에는 GENERATED_BODY 매크로가 포함되어 있으며, MyClass.generated.h 파일이 #include 되어 있습니다.
* MyClass.generated.h 파일은 UHT가 생성하는 헤더 파일로, GENERATED_BODY 매크로를 통해 자동으로 생성된 코드를 포함합니다.

<br>

* 위 예시에서는 UCLASS 매크로를 사용하여 UClass인 AMyClass를 정의하고 있습니다.
* GENERATED_BODY 매크로는 클래스 정의의 끝 부분에 위치하며, 클래스의 추가 코드를 자동으로 생성합니다.
* 이를 통해 Reflection System, Blueprint System, Garbage Collection System 등에서 사용되는 코드를 자동으로 생성할 수 있습니다.

<br>

* 또한, MyClass.generated.h 파일은 MyClass.h 파일 내에 #include 되어 있으며, GENERATED_BODY 매크로를 통해 자동으로 생성된 코드를 포함합니다.
* 이 파일은 빌드 시간을 줄이는 데도 기여합니다.

<br>

GENERATED_BODY와 .generated.h의 장점
===============
* GENERATED_BODY 매크로와 .generated.h 파일은 UHT가 생성하는 코드의 중요한 부분입니다. * 이들을 사용하면, 클래스의 추가 코드를 자동으로 생성하여 Reflection System, Blueprint System, Garbage Collection System 등에서 사용할 수 있습니다.
* 이는 개발자가 코드를 작성하는 데 드는 노력을 줄이고, 코드의 가독성과 유지 보수성을 향상시킵니다.

<br>

* 또한, .generated.h 파일은 클래스 선언 파일에서 필요한 코드만 포함시키므로, 빌드 시간을 줄이는 데도 기여합니다.
* 이는 프로젝트의 빌드 속도를 향상시켜 개발자들이 보다 효율적으로 작업할 수 있도록 도와줍니다.

* 따라서, GENERATED_BODY 매크로와 .generated.h 파일은 언리얼 엔진에서 코드 생성 시스템의 중요한 부분으로, 개발자가 보다 간편하게 코드를 작성하고, 프로젝트를 빠르게 빌드할 수 있도록 도와줍니다.