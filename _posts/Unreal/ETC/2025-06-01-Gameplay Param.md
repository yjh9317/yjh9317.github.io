---
title: GameplayTag Param
date: 2025-06-01
categories: [unreal, Unreal Etc]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

* GameplayTag 매개변수에 `UPARAM(meta = (Categories = ""))` 를 이용하면 블루프린트에서 Categoriees에 해당하는 Tag의 하위 Tag만 뜨도록 설정 가능

```c++
UFUNCTION(BlueprintCallable)
void TestFunc(UPARAM(meta = (Categories = "Shared.Attack")) FGameplayTag FirstTag, UPARAM(meta = (Categories = "Shared.Movement")) FGameplayTag SecondTag);
```

* FirstTag는 Shared.Attack 에서 파생되는 하위 Tag만 접근이 가능하고 
* SecondTag는 Shared.Movement에서 파생되는 하위 Tag만 접근이 가능
