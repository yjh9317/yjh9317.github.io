---
title: Sync Content Browser To Asset
date: 2023-09-19
categories: [unreal,Editor]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Sync Content Browser To Asset**
==============

* SListView에서 어떤 에셋을 클릭하면 해당 에셋이 있는 경로로 가려는 방식으로 만들려 한다.

* 먼저 SListView의 함수 중에서 OnMouseButtonClicked이라는 델리게이트 함수를 추가해서 List에서 Asset을 클릭하면 함수가 호출되게 만들 수 있다.


```c++
TSharedRef<SListView<TSharedPtr<FAssetData>>> SAdvanceDeletionTab::ConstructAssetListView()
{
	ConstructedAssetListView = SNew(SListView<TSharedPtr<FAssetData>>)
	.ItemHeight(24.f)	
	.ListItemsSource(&DisplayedAssetsData)
	.OnGenerateRow(this,&SAdvanceDeletionTab::OnGenerateRowForList)
	.OnMouseButtonClick(this,&SAdvanceDeletionTab::OnRowWidgetMouseButtonClicked); // 이부분 추가

	return ConstructedAssetListView.ToSharedRef(); 
}
```

<br>

* 그 이후에 `UEditorAssetLibrary::SyncBrowserToObjects` 함수를 이용해서 Content Brower에서 함수의 매개변수로 들어오는 Asset의 경로값으로 이동하게 만들 수 있다.

```c++
void SAdvanceDeletionTab::OnRowWidgetMouseButtonClicked(TSharedPtr<FAssetData> ClickedData)
{
    FSuperManagerModule& SuperManagerModule = 
        FModuleManager::LoadModuleChecked<FSuperManagerModule>(TEXT("SuperManager"));

	SuperManagerModule.SyncCBToClickedAssetForAssetList(ClickedData->GetSoftObjectPath().ToString());
}


// 모듈
void FSuperManagerModule::SyncCBToClickedAssetForAssetList(const FString& AssetPathToSync)
{
	TArray<FString> AssetsPathToSync;
	AssetsPathToSync.Add(AssetPathToSync);
	
	UEditorAssetLibrary::SyncBrowserToObjects(AssetsPathToSync);
}
```