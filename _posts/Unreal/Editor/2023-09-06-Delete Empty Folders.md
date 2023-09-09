---
title: Delete Empty Folders
date: 2023-09-06
categories: [unreal,Editor]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

* Custom Menu Entry 장에서 사용했던 AddCBMenuEntry함수에 다른 Menu Entry를 넣어서 비어있는 폴더를 지울 버튼을 추가한다.

```c++
// .h
void OnDeleteEmptyFoldersButtonClicked(); // 비어있는 폴더를 삭제할 함수 추가

// cpp
void FSuperManagerModule::AddCBMenuEntry(FMenuBuilder& MenuBuilder)
{
	...

	MenuBuilder.AddMenuEntry
	(
	FText::FromString(TEXT("Delete Empty Folders")),
	FText::FromString(TEXT("Safely delete all empty folders")),
	FSlateIcon(),	
	FExecuteAction::CreateRaw(
            this, &SuperManagerModule::OnDeleteEmptyFoldersButtonClicked)
	);
}
```

<br>

## 언리얼에서 지원하는 함수

* 아래 함수들을 이용해서 폴더를 지우는 함수를 작성한다

```c++
// EditorAssetLibrary.h

// 해당 경로에 있는 에셋 목록을 가져오는 함수
UFUNCTION(BlueprintCallable, Category = "Editor Scripting | Asset")
static TArray<FString> ListAssets(const FString& DirectoryPath, 
bool bRecursive = true, bool bIncludeFolder = false);

// 컨텐츠 브라우저에 해당 경로가 존재하는지 확인하는 함수
UFUNCTION(BlueprintCallable, Category = "Editor Scripting | Asset")
static bool DoesDirectoryExist(const FString& DirectoryPath);

// 해당 경로에 에셋이 존재하는지 확인하는 함수
UFUNCTION(BlueprintCallable, Category = "Editor Scripting | Asset")
static bool DoesDirectoryHaveAssets(const FString& DirectoryPath, bool bRecursive = true);
```

<br>

## 폴더를 지우는 함수

```c++
void FSuperManagerModule::OnDeleteEmptyFoldersButtonClicked()
{
	FixupRedirectors();
	
    // 세 번째 매개변수가 true여야 folder도 포함한다.
	TArray<FString> FolderPathsArray = 
        UEditorAssetLibrary::ListAssets(FolderPathSelected[0], true, true);

	uint32 Counter = 0;

	FString EmptyFolderPathsNames;
	TArray<FString> EmptyFoldersPathsArray;

	for(const FString& FolderPath : FolderPathsArray)
	{
		if(FolderPath.Contains(TEXT("Developers")) ||
			FolderPath.Contains(TEXT("Collections")) ||
			FolderPath.Contains(TEXT("__ExternalActors__")) ||
			FolderPath.Contains(TEXT("__ExternalObjects__")))
		{
			continue;
		}

		if(!UEditorAssetLibrary::DoesDirectoryExist(FolderPath)) continue;


		if(UEditorAssetLibrary::DoesDirectoryHaveAssets(FolderPath))
		{
            // 메세지에 사용할 FString
			EmptyFolderPathsNames.Append(FolderPath);
			EmptyFolderPathsNames.Append(TEXT("\n"));

            // 삭제할 폴더 배열
			EmptyFoldersPathsArray.Add(FolderPath);
		}
	}

	if(EmptyFoldersPathsArray.Num() == 0)   // 삭제할 폴더 없음
	{
		DebugHeader::ShowMsgDialog(EAppMsgType::Ok,
            TEXT("No empty folder found under selected folder"),false);
		return;
	}

	EAppReturnType::Type ConfirmResult =
	DebugHeader::ShowMsgDialog(EAppMsgType::OkCancel,TEXT("Empty folders found in:\n") +
             EmptyFolderPathsNames + TEXT("\nWould you like to delete all?"),false);

	if(ConfirmResult == EAppReturnType::Cancel) return; // 취소

	for(const FString& EmptyFolderPath : EmptyFoldersPathsArray)
	{
        // DeleteDirectory 함수는 성공하면 true 실패하면 false를 반환한다.
        // 삼항 연산자로 반환값에 따라 알맞게 대처
		UEditorAssetLibrary::DeleteDirectory(EmptyFolderPath) ?
			++Counter :
			DebugHeader::Print(TEXT("Failed to delete " + EmptyFolderPath),FColor::Red);
	}

	if(Counter > 0)
	{
		DebugHeader::ShowNotifyInfo(TEXT("Successfully deleted ") + 
            FString::FromInt(Counter) +TEXT("Folders"));
	}
}
```