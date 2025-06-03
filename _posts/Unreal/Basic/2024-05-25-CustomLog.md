---
title: Custom Log
date: 2024-05-25
categories: [unreal, Basic]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---


* 언리얼에서 새로운 카테고리의 로그를 생성하려면 `DECLARE_LOG_CATEGORY_EXTERN` 매크로를 사용해야 한다.

# DECLARE_LOG_CATEGORY_EXTERN

*  특정 로그 카테고리의 존재를 헤더 파일 등에 선언해준다. 이렇게 선언된 로그 카테고리는 여러 소스 파일에서 접근할 수 있으며, 실제 정의는 다른 곳에서 제공한다.

* 보통 위 매크로는 선언해주는 것이기 때문에 헤더파일에서 작성한다.

```c++
// 첫 번째 파라미터는 카테고리 이름,
// 두 번째 파라미터는 기본 로그 출력 형식(예: Log, Warning, Error 등),
// 세 번째 파라미터는 컴파일 타임 로깅 플래그 (예: All, None 등)
// 보통 헤더 파일에 위치하며, 여러 소스 파일에서 접근할 수 있다.
DECLARE_LOG_CATEGORY_EXTERN(MyLogCategory, Log, All);
```

<br>

# DEFINE_LOG_CATEGORY

* 위에서 선언한 로그를 실제 정의할 때 사용하는 매크로

* 소스파일에서 작성한다.

```c++
// 실제로 로그 카테고리를 하나의 소스 파일에서 정의하여 메모리에 할당
// 반드시 하나의 소스 파일(.cpp)에서 호출
DEFINE_LOG_CATEGORY(MyLogCategory);
```

*  MyLogCategory에 해당하는 로그 변수가 메모리에 생성되며, UE_LOG 매크로 등을 통해 로그 메시지를 출력할 수 있다.