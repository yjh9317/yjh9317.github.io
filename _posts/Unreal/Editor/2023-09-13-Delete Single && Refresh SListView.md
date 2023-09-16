---
title: Delete Single && Refresh SListView
date: 2023-09-13
categories: [unreal,Editor]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

**Delete Single Asset**
============

* 이전 장에서 만들었던 `SButton`으로 해당 에셋을 `ObjectTools::DeleteAssets()` 함

```c++
// 모듈
bool FSuperManagerModule::DeleteSingleAssetForAssetList(const FAssetData& AssetDataToDelete)
{
	TArray<FAssetData> AssetDataForDeletion;
	AssetDataForDeletion.Add(AssetDataToDelete);
	
	if(ObjectTools::DeleteAssets(AssetDataForDeletion) > 0)
	{
		return true;
	}
	return false;
}
```

<br>

```c++
// 슬레이트
FReply SAdvanceDeletionTab::OnDeleteButtonClicked(TSharedPtr<FAssetData> ClickedAssetData)
{
	// 모듈 불러오기
	FSuperManagerModule& SuperManagerModule = 
	FModuleManager::LoadModuleChecked<FSuperManagerModule>(TEXT("SuperManager"));

	// 모듈에게 에셋 삭제 요청
    // 여기서 ObjectTools::DeleteAssets()를 쓸 수 있지만 관리적인 측면에서 모듈에서 실행하도록 만듦
	const bool BAssetDeleted = 
        SuperManagerModule.DeleteSingleAssetForAssetList(*ClickedAssetData.Get());

	if(BAssetDeleted)
	{
		// Updating the list source items
		if(StoredAssetsData.Contains(ClickedAssetData))
		{
			StoredAssetsData.Remove(ClickedAssetData);
		}

		// Refresh the list
		RefreshAssetListView();
	}
	 
	return FReply::Handled();	
}
```

<br>

**Refresh**
=========

* 커스텀 에디터에서 삭제버튼을 눌러 해당 에셋을 삭제한다고 해도 커스텀 에디터에서는 Refresh하지 않아 그대로인 상태로 있기 때문에 SListView를 Refresh를 해줘야 한다

* 이 때 사용하는 함수가 `SListView의 RebuildList()`이다.

```c++
// Refresh 함수
void SAdvanceDeletionTab::RefreshAssetListView()
{
	if(ConstructedAssetListView.IsValid())
	{
		ConstructedAssetListView->RebuildList();
	}
}
```


<br>

**사진**
=========

* 삭제 버튼 누르면 ObjectTools::DeleteAssets 함수 호출이 됨

<center><img src="./../../../assets/img/Unreal/Editor/Refresh%20SListView/DeleteBefore.png" style="width: 90%; height: auto;"></center>

* 지우고 나서 Refresh되어서 삭제된 에셋이 리스트에 없음

<center><img src="./../../../assets/img/Unreal/Editor/Refresh%20SListView/DeleteAfter.png" style="width: 90%; height: auto;"></center>
