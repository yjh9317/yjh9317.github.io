---
title: TOptional
date: 2024-05-10
categories: [unreal, Basic]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# TOptional

* C++17의 std::optional과 유사한 역할을 하는 템플릿 클래스

* 특정 값이 있을 수도 있고 없을 수도 있는 상황을 표현할 때 사용

### 주로 사용하는 곳

* 함수 반환값
* 초기화되지 않은 상태의 표현
* 구성 옵션(게임 설정이나 런타임 옵션 등에서 값이 필수적이지 않은 옵션들)
* 비동기 작업의 결과

### 함수

* `IsSet()` : 값이 있는지 확인하는 함수
* `Reset()` : 갖고 있는 값을 삭제하는 함수

<br>

## 예시

```c++
TOptional<FString> FindPlayerNameByID(int32 PlayerID)
{
    // 예제: 플레이어 ID로 플레이어 이름을 찾는 로직
    FString FoundName;
    bool bPlayerFound = /* 플레이어 검색 로직 */ false;

    if (bPlayerFound)
    {
        // 플레이어 이름이 발견된 경우 Optional에 값 할당
        return TOptional<FString>(FoundName);
    }
    else
    {
        // 값이 없는 경우 빈 Optional 반환
        return TOptional<FString>();
    }
}

void TestPlayerNameSearch()
{
    TOptional<FString> PlayerNameOpt = FindPlayerNameByID(1001);

    if (PlayerNameOpt.IsSet())
    {
        UE_LOG(LogTemp, Log, TEXT("플레이어 이름: %s"), *PlayerNameOpt.GetValue());
    }
    else
    {
        UE_LOG(LogTemp, Warning, TEXT("해당 ID를 가진 플레이어를 찾을 수 없습니다."));
    }
}
```