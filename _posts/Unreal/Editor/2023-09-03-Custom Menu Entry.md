---
title: Custom Menu Entry
date: 2023-09-03
categories: [unreal,Editor]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Custom Menu Entry**
==============

* 델리게이트를 이용하여 에디터의 메뉴 엔트리에 버튼을 추가할 수 있다.

* build.cs 파일에서 `"ContentBrowser"`를 넣어줘야 한다.

 ```c++
 void FSuperManagerModule::InitCBMenuExtention()
{
    // 컨텐츠 브라우저 모듈을 가져온다,
	FContentBrowserModule& ContentBrowserModule = 
	FModuleManager::LoadModuleChecked<FContentBrowserModule>(TEXT("ContentBrowser"));

    // 컨텐츠 브라우저의 모든 메뉴 확장 컨텍스트를 가져온다
	TArray<FContentBrowserMenuExtender_SelectedPaths>& 
    ContentBrowserModuleMenuExtenders = ContentBrowserModule.GetAllPathViewContextMenuExtenders();

	// 델리게이트 추가
	ContentBrowserModuleMenuExtenders.Add
        (FContentBrowserMenuExtender_SelectedPaths::CreateRaw
		    (this,&FSuperManagerModule::CustomCBMenuExtender));
}


// 델리게이트로 사용할 함수
TSharedRef<FExtender> FSuperManagerModule::CustomCBMenuExtender(const TArray<FString>& SelecetedPaths)
{
	TSharedRef<FExtender> MenuExtender(new FExtender());

	if(SelecetedPaths.Num() > 0)
	{
		MenuExtender->AddMenuExtension(FName("Delete"), // 추가할 메뉴 엔트리 이름
		EExtensionHook::After,                          // 위치
		TSharedPtr<FUICommandList>(),                   // 단축키
		FMenuExtensionDelegate::CreateRaw
            (this,&FSuperManagerModule::AddCBMenuEntry)); // 델리게이트
	}
	
	return MenuExtender;
}


// 추가할 메뉴 엔트리
void FSuperManagerModule::AddCBMenuEntry(FMenuBuilder& MenuBuilder)
{
	MenuBuilder.AddMenuEntry
	(
		FText::FromString(TEXT("Delete UnUsed Assets")),		            // 타이틀
		FText::FromString(TEXT("Safely delete all unused assets under folder")),// 툴팁
		FSlateIcon(),	                                                    // 아이콘
		FExecuteAction::CreateRaw
        	(this,&FSuperManagerModule::OnDeleteUnusedAssetbuttonClicked) // 델리게잍
	);
}
 ```


* 다음과 같이 메뉴 엔트리에 추가된다

<center><img src="./../../../assets/img/Unreal/Editor/Custom%20Menu%20Entry/DeleteUnusedAssetButton.png" style="width: 70%; height: auto;"></center>


<br>

**개발자 훅**
==========

* `UI 메뉴, 메뉴바 및 툴바를 확장할 수 있게 허용하는 개발자 훅`을 위 사진같이 `초록색 글자로 표시`할 수 있다.

* 편집->에디터 설정에서 다음과 같이 검색한 후 체크해주고 에디터를 재시작하면 된다.

<center><img src="./../../../assets/img/Unreal/Editor/Custom%20Menu%20Entry/UI Extension.png" style="width: 70%; height: auto;"></center>


