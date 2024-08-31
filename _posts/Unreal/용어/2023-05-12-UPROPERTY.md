---
title: UPROPERTY
date: 2023-05-12
categories: [unreal, 용어]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

UPROPERTY
==============

* 변수와 같이 사용하는 리플렉션 매크로로, 매크로의 인자에 값을 넣어 여러가지 방면으로 사용할 수 있다.

<br>

## 수정 및 공개


* 에디터나 월드에서 변수를 수정할 수 있게 하거나 어디까지 공개할지를 정하는 프로퍼티



* Visible과 Edit은 변수의 수정 가능 여부를 의미한다.

  * Visible이 사용되면 그 변수는 Read만 가능하다
  * Edit이 사용되면 그 변수는 Read & Write이 가능하다



* DefaultsOnly는 , InstanceOnly는, Anywhere은 변수 공개 범위를 의미한다.

    * DefaultsOnly는 블루 프린트의 에디터에서만 공개함을 의미한다

    * InstanceOnly는 월드 상에서 존재하는 오브젝트에서만 공개함을 의미한다.

    * Anywhere은 Defaults와 Instance를 합친 것으로, 에디터와 월드 둘 다 접근 가능하다


* 위를 토대로 작성해보면

```
* Visible

VisibleDefaultsOnly : 블루프린트 에디터 창의 디테일 패널에서 값을 보기 가능

VisibleInstanceOnly : 월드상에 배치된 오브젝트의 디테일 패널에서 값을 보기 가능

VisibleAnywhere : 블루프린트 에디터 & 월드상에 배치된 오브젝트의 디테일 패널에서 값을 보기 가능

* Edit
 
EditDefulatOnly : 블루프린트 에디터 창의 디테일 패널에서 값을 수정 가능

EditInstanceOnly : 월드상에 배치된 오브젝트의 디테일 패널에서 값을 수정 가능

EditAnywhere : 블루프린트 에디터 & 월드상에 배치된 오브젝트의 디테일 패널에서 값을 수정 가능
```

<br>

## 블루 프린트 


* 블루 프린트에서 변수를 수정할 수 있게 하거나 어디까지 공개할지를 정하는 프로퍼티

<br>

* 총 4가지로 아래와 같이 있다

```
* BlueprintReadOnly : 블루프린트에서 해당 변수를 읽기

* BlueprintReadWrite : 블루프린트에서 해당 변수를 읽기 & 쓰기

* BlueprintGetter :	해당 변수에 접근 할 수 있는 함수를 지정하고 블루프린트는 해당 함수를 통해 변수에 접근

* BlueprintSetter :	해당 변수에 수정 할 수 있는 함수를 지정하고 블루프린트는 해당 함수를 통해 변수에 수정
```



## Category


* 카테고리를 이용해 에디터상에서 변수들의 카테고리를 정해줄 수 있다

* UPROPERTY(EditAnywhere, Category = "Player Stats")




## Meta


* 에디터 관련 여러가지 기능을 추가할 수 있다

### DisplayName

* 에디터에서 해당 속성이 표시될 때 사용할 이름을 지정

* Meta = (DisplayName = "Player Health")

### ToolTip

* 에디터에서 마우스를 속성 위에 올렸을 때 나타나는 툴팁

* Meta = (ToolTip = "The health of the player character")


### ClampMin/ClampMax

* 변수의 값을 특정 범위로 제한

* Meta = (ClampMin = "0.0", ClampMax = "100.0")

### AllowPrivateAccess

* 매개변수는 private 변수임에도 불구하고 에디터에서 접근 가능하게 만듦

* Meta = (AllowPrivateAccess = "true")

### HideInDetailPanel

* 에디터의 디테일 패널에서 이 속성을 숨긴다.

* Meta = (HideInDetailPanel)

### ExposeOnSpawn

* 속성을 블루프린트의 "노드 스폰"에서 초기값으로 설정, 특정 객체가 생성될 때 초기화해야 하는 속성을 지정할 때 유용

* Meta = (ExposeOnSpawn)

### UIMin,UIMax

* UPROPERTY에 대한 UI 범위를 설정하는 데 사용되는 매크로

* 변수 값의 슬라이더나 스핀 박스의 최소값과 최대값을 설정하는 데 사용

* Meta = (UIMin = "0.0", UIMax = "100.0")

### BindWidget

* 언리얼 엔진에서 UMG(언리얼 모션 그래픽) 위젯을 C++ 코드에 바인딩하는 데 사용되는 매크로

* Meta = (BindWidget)
  * 단 블루프린트에서의 변수 이름과 C++의 변수이름은 동일해야 한다.

### InlineEditConditionToggle

* EditCondition과 함께 사용되어, 조건을 설정하는 변수에 대한 토글 버튼을 에디터에 표시

* Meta = (InlineEditConditionToggle)

### DisplayPriority

* 에디터의 디테일 패널에서 속성이 표시되는 순서를 결정

* Meta = (DisplayPriority = 1)

### MultiLine

* 에디터에서 텍스트를 입력할 때 여러 줄의 입력을 허용하는 옵션

* Meta = (MultiLine = "true")

### HideAlphaChannel

* 색상에서 알파값을 숨김

* Meta = (HideAlphaChannel)

### PinHiddenByDefault

* 블루프린트 에디터에서 해당 속성이 기본적으로 핀으로 노출되지 않도록 설정

* Meta = (PinHiddenByDefault)

### TitleProperty

* 배열이나 맵 같은 복합 데이터 구조에서, 해당 구조체 또는 클래스의 특정 멤버 변수를 배열 요소의 이름으로 표시

* Meta = (TitleProperty = "Name")