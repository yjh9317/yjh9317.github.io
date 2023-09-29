---
title: Editor Utility Widget
date: 2023-09-25
categories: [unreal,Editor]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Editor Utility Widget**
=========

* Editor Utility Widget은 UMG(Unreal Motion Graphics)에 기반한 Slave Widget이다.

* 에디터 탭을 만들기에 유저 친화적이며 블루프린트에 노출(Expose)이 된다.


### 위젯 만들기

* 일단 컨텐츠 브라우저에 가서 마우스 우클릭한 다음에, `에디터 유틸리티 -> 에디터 유틸리티 위젯`을 생성하고 다음과 같이 만들어준다.


<center><img src="./../../../assets/img/Unreal/Editor/Editor%20Utility%20Widget/Tree.png" style="width: 70%; height: auto;"></center>

* 캔버스 패널 
  * 가로 상자
    * 디테일뷰
    * 버튼 - 텍스트

### 클래스 만들기

* 그리고 다음과 같이 EditorUtilityWidget을 상속받은 클래스를 만들어준다.

```c++
UCLASS()
class SUPERMANAGER_API UQuickMaterialCreationWidget : public UEditorUtilityWidget
{
	GENERATED_BODY()

public:

#pragma region QuickMaterialCreationCore

	UFUNCTION(BlueprintCallable)
	void CreateMaterialFromSelectedTextures();

	UPROPERTY(EditAnywhere,BlueprintReadWrite, Category="CreateMaterialFromSelectedTextures")
	FString MaterialName = TEXT("M_");

#pragma endregion 
};
```

```c++
#include "DebugHeader.h"

void UQuickMaterialCreationWidget::CreateMaterialFromSelectedTextures()
{
	DebugHeader::Print(TEXT("Working"),FColor::Cyan);
}
```

* 그리고 위젯에서 디테일뷰에 가서 `표시할 카테고리`를 생성해서 인덱스에 사용할 변수와 동일한 카테고리를 넣어줘야 디테일 뷰에서 해당 변수를 사용할 수 있다.

* 그리고 블루프린트에서 다음과 같이 추가한다.


<center><img src="./../../../assets/img/Unreal/Editor/Editor%20Utility%20Widget/Create Material Blueprint.png" style="width: 70%; height: auto;"></center>

<br>

**결과**
=========

<center><img src="./../../../assets/img/Unreal/Editor/Editor%20Utility%20Widget/Create Material Button.png" style="width: 70%; height: auto;"></center>


* 디테일 뷰에 Material Name이 들어갔고 Button으로 CreateMaterialFromSelectedTextures 함수 호출