---
title: Delete Unused Asset && Fixup Redirector
date: 2023-09-02
categories: [unreal,Editor]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Delete Unused Asset**
===========

* 사용중이지 않은 에셋을 삭제하려면 일단 해당 에셋이 사용중인지 확인해야한다.

* 그럴때 사용하는 함수가 `FindPackageReferencersForAsset`읻다.

```c++
// AssetPath 는 찾고자 하는 에셋 경로
// bLoadAssetsToConfirm는 종속성을 확인하기 위해 에셋과 레퍼런스를 로드할지 결정
UFUNCTION(BlueprintCallable, Category = "Editor Scripting | Asset")
static TArray<FString> FindPackageReferencersForAsset(
        const FString& AssetPath, bool bLoadAssetsToConfirm = false);
```

* 언리얼에는 종속성에 `강 참조(Hard), 약 참조(Soft)`가 있다.

  * 강 참조(Hard)는 A오브젝트가 B오브젝트를 참조하고 있다면, A오브젝트 로드시 B 오브젝트도 로드하는 방식이다
  * 약 참조(Soft)는 경로(Path)같은 문자열 형태의 간접 매커니즘을 통해 A오브젝트가 B오브젝트를 참조하게 만들어 로드하는 방식이다.



```c++
#include "ObjectTools.h"

// 소스파일
void UQuickAssetAction::RemoveUnusedAssets()
{
    // 선택된 에셋 데이터 배열
	TArray<FAssetData> SelectedAssetsData = 
			UEditorUtilityLibrary::GetSelectedAssetData();

	// 삭제할 에셋 데이터 배열
	TArray<FAssetData> UnusedAssetsData;

	FixupRedirectors();
	
	for(const FAssetData& SelecetedAssetData : SelectedAssetsData)
	{

		TArray<FString> AssetReferencers =  
            UEditorAssetLibrary::FindPackageReferencersForAsset
            (SelecetedAssetData.GetSoftObjectPath().ToString());

		if(AssetReferencers.Num() == 0) // 에셋의 참조횟수가 0이라면
		{
			UnusedAssetsData.Add(SelecetedAssetData);
		}
	}

	if(UnusedAssetsData.Num() == 0) // 삭제할 에셋이 없다면 메세지 출력
	{
		ShowMsgDialog(EAppMsgType::Ok,
				TEXT("No unused asset found among seleceted assets"),
				false);
		return;
	}

    // DeleteAssets 함수로 삭제 및 총 삭제한 개수를 리턴
	const int32 NumOfAssetsDeleted = ObjectTools::DeleteAssets(UnusedAssetsData);

	if(NumOfAssetsDeleted == 0) return;

	ShowNotifyInfo(TEXT("Successfully deleted ") + 
				FString::FromInt(NumOfAssetsDeleted) +
				TEXT(" unused assets"));
}
```

<br>

**Fixup Redirector**
===============

* `Redirector는 이동된 애셋을 가리키던 레퍼런스를 현재 위치로 고쳐주는 오브젝트`이다.

* 만약 에셋의 위치를 옮기거나 이름을 바꾸면 원래 있던 장소에 Redirector가 남는다.

* 그렇기 때문에 Redirector 오브젝트를 갱신하지 않으면 에셋을 이동한 후에 RemoveUnUsedAssets 함수를 호출해도 작동하지 않는다.

<br>

<center><img src="./../../../assets/img/Unreal/Editor/DeleteAndRedirector/Editor%20Redirector.png" style="width: 100%; height: auto;"></center>


* 에디터에도 이 Redirector를 지원하지만, 매번 옮길때마다 에디터에서 이 함수를 호출하기에는 효율적이지 않아 C++로 작성해서 호출시키는 방식으로 만든다.

```c++
#include "AssetRegistry/AssetRegistryModule.h"
#include "AssetToolsModule.h"

void UQuickAssetAction::FixupRedirectors()
{
	TArray<UObjectRedirector*> RedirectorsToFixArray;

    // LoadModuleChecked로 AssetRegistry 모듈이 존재하는지 확인
	FAssetRegistryModule& AssetRegistryModule = 
		FModuleManager::Get().
		LoadModuleChecked<FAssetRegistryModule>(TEXT("AssetRegistry"));


	FARFilter Filter;
	Filter.bRecursivePaths = true;                  // 하위 폴더의 접근 허용
	Filter.PackagePaths.Emplace("/Game");           // 경로, ("/Game")은 컨텐츠 브라우저를 의미
	Filter.ClassPaths.Emplace("ObjectRedirector");  // 가져올 클래스 이름


	TArray<FAssetData> OutRedirectors;
	

    // 필터에 해당하는 에셋 데이터를 전부 반환
	AssetRegistryModule.Get().GetAssets(Filter, OutRedirectors);    


	for(const FAssetData& RedirectorData : OutRedirectors)
	{
		if(UObjectRedirector* RedirectorToFix = 
				Cast<UObjectRedirector>(RedirectorData.GetAsset()))
		{
			RedirectorsToFixArray.Add(RedirectorToFix);
		}
	}


	FAssetToolsModule& AssetToolsModule = 
	FModuleManager::LoadModuleChecked<FAssetToolsModule>(TEXT("AssetTools"));


    // 고쳐야할 Redirector을 수정
	AssetToolsModule.Get().FixupReferencers(RedirectorsToFixArray);
}
```

* AssetToolsModule을 사용하려면 build.cs 파일에서 "AssetTools"를 추가해야 한다.

