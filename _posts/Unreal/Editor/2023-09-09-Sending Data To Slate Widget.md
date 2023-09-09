---
title: Sending Data To Slate Widget
date: 2023-09-09
categories: [unreal,Editor]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---


* Slate Widget에게 데이터를 보내기 위해서는 `SLATE_ARGUMENT`라는 매크로를 사용해야 한다.

  * `SLATE_BEGIN_ARGS`와 `SLATE_END_ARGS`사이에 넣어야 함

```c++
SLATE_ARGUMENT( ArgType, ArgName )
```

* 그러면 위젯이 생성될 때, ArgType타입에 대한 ArgName이 같이 생성된다.

```c++
#pragma once

#include "Widgets/SCompoundWidget.h"

class SAdvanceDeletionTab : public SCompoundWidget
{
	SLATE_BEGIN_ARGS(SAdvanceDeletionTab) {}

	SLATE_ARGUMENT(FString,TestString) // FString 인자를 가진 TestString 선언
	
	SLATE_END_ARGS()

public:
	void Construct(const FArguments& InArgs);
	
};

```

* 다음과 같이 만들면 다른 클래스에서 이 위젯 클래스에게 데이터를 보낼 때 사용할 수 있다.
  
```c++
// 
TSharedRef<SDockTab> FSuperManagerModule::OnSpawnAdvanceDeletionTab(const FSpawnTabArgs& SpawnTabArgs)
{
	// SDockTab(Slate) 생성 후 반환
	return SNew(SDockTab).TabRole(ETabRole::NomadTab)
	[
		SNew(SAdvanceDeletionTab) 	// 괄호 안의 클래스 이름의 Slate 생성
		.TestString(TEXT("I am passing data")) // SLATE_ARGUMENT으로 선언된 TestString에 값 전달
	];
}
```

* 여기서 `대괄호 []` 는 `하나의 슬롯을 의미한다`.

* 위 함수는 블루프린트 에디터에서 계층 구조에 텍스트를 하나 넣은 것과 동일하다




<br>

받은 값 사용
============

* Slate Widget 클래스 생성자의 매개변수 `const FArguments& InArgs`에는 `SLATE_ARGUMENT` 매크로에서 사용했던 이름(TestString)과 _가 합쳐 `_이름`의 형태로 저장돼 있다.

```c++
void SAdvanceDeletionTab::Construct(const FArguments& InArgs)
{
	bCanSupportFocus = true;
    
    // ChildSlot은 Slate Widget의 기본 슬롯
	ChildSlot
	[
		SNew(STextBlock) // Slate 텍스트 생성
		.Text(FText::FromString(InArgs._TestString)) // 텍스트 전달
	];
}
```

<br>

사진
-------

* i am passing data가 적힌 모습


<center><img src="./../../../assets/img/Unreal/Editor/SendingDataToSlate/passing%20data.png" style="width: 70%; height: auto;"></center>

