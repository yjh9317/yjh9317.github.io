---
title: TEnumRange
date: 2024-06-18
categories: [unreal, Basic]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# TEnumRange

* 특정 Enum의 모든 값들을 반복문으로 돌리고 싶을 때 사용

* `ENUM_RANGE_BY_COUNT` 매크로를 선언해야 가능함

* `#include "Misc/EnumRange.h"` 헤더파일이 필요함

```c++
UENUM()
enum class EItemSlot
{
	NONE,
	HEAD,	
	GLOVES,
};

ENUM_RANGE_BY_COUNT(EItemSlot, EItemSlot::GLOVES);

for (EItemSlot ItemSlot : TEnumRange<EItemSlot>())
{
    // ItemSlot : NONE부터 GLOVES까지 차례대로 반복문 실행
}
```