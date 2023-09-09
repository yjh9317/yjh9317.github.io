---
title: Search and Delete unused
date: 2023-09-04
categories: [unreal,Editor]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

* `Custom Menu Entry`에서 사용했던 `CustomCBMenuExtender 함수의 매개변수`는 `경로`이므로 이 경로를 헤더 파일에 따로 저장하고 다음 함수에서 사용한다.

* 함수에서 보기 불필요한 메세지는 제외함

```c++
// TArray<FString> FolderPathSelected

void FSuperManagerModule::OnDeleteUnusedAssetbuttonClicked()
{
    // 한 번에 한 폴더만 하기 위해 1개 초과는 X
	if(FolderPathSelected.Num() > 1) return; 

    // ListAssets은 디렉토리 경로에서 찾은 모든 에셋 목록을 반환
	TArray<FString> AssetsPathNames = 
            UEditorAssetLibrary::ListAssets(FolderPathSelected[0]);

	if(AssetsPathNames.Num() == 0) return;

	EAppReturnType::Type ConfirmResult = 
	    DebugHeader::ShowMsgDialog(EAppMsgType::YesNo, TEXT("~내용~"),false); 

	if(ConfirmResult == EAppReturnType::No) return;

	FixupRedirectors();

	TArray<FAssetData> UnusedAssetsDataArray;

	for(const FString& AssetPathName : AssetsPathNames)
	{
		// 지우면 안되는 최상위 폴더는 삭제 안되게 넘어간다.
		if(AssetPathName.Contains(TEXT("Developers")) ||
			AssetPathName.Contains(TEXT("Collections")) ||
			AssetPathName.Contains(TEXT("__ExternalActors__")) ||
			AssetPathName.Contains(TEXT("__ExternalObjects__")))
		{
			continue;
		}
		
		if(!UEditorAssetLibrary::DoesAssetExist(AssetPathName)) continue;

        // 해당 에셋이 참조되는지 확인
		TArray<FString> AssetReferencers = 
		UEditorAssetLibrary::FindPackageReferencersForAsset(AssetPathName);

        // 에셋의 참조가 0이라면 따로 저장
		if(AssetReferencers.Num() == 0)
		{
			const FAssetData UnusedAssetData = 
                UEditorAssetLibrary::FindAssetData(AssetPathName);

			UnusedAssetsDataArray.Add(UnusedAssetData);
		}
	}

	if(UnusedAssetsDataArray.Num() > 0) // 삭제
	{
		ObjectTools::DeleteAssets(UnusedAssetsDataArray);
	}
}
```

<br>
