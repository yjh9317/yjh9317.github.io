---
title: Widget Stack
date: 2025-06-01
categories: [unreal, CommonUI]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Widget Stack

* Common UI에서는 UI 요소(위젯)들을 직접 생성하기보다 "스택"에 밀어 넣어(push) 관리

* 게임에서 일반적으로 4가지 형식의 스택을 사용

### 1. Modal Stack

* 각종 팝업창(예: 알림, 확인 창)을 위한 최상위 스택

### 2. Game Menu Stack

* 인벤토리나 게임 내 메뉴(설정, 퀘스트 창 등)를 위한 스택

### 3. Game HUD Stack

* 체력 바처럼 플레이어가 직접 상호작용하지 않는 화면 정보 표시용 스택

### 4. Front end Stack

* `Press Any Key` 같은 초기 화면 UI를 위한 최하위 스택

<br>

# Widget Stack 순서의 중요성

* 상위 스택의 위젯은 하위 스택의 위젯을 비활성화하거나 덮을 수 있다.

* 우선순위 및 비활성화 기능은 UI 시스템을 효과적으로 만드는 데 핵심적인 역할

### 예시 1. 같은 스택 내

* Modal Stack에서 팝업창2가 팝업창1보다 나중에 표시되면, 팝업창2가 팝업창1의 상호작용을 막을 수 있음

### 예시 2. 다른 스택 간

* Modal Stack에 있는 위젯은 Game Menu Stack이나 Front end Stack에 있는 위젯보다 우선권을 가짐

<br>

# 스택 찾는 방법 : GameplayTag

* 여러 개의 스택 중 원하는 스택에 정확히 위젯을 추가하려면 각 스택을 식별할 방법이 필요하기 때문에 `GameplayTag`를 사용

* 각 위젯 스택에 고유한 게임플레이 태그를 ID처럼 할당

### 스택의 Gameplay Tag 예시

* `Modal Stack` → Frontend.WidgetStack.Modal
* `Game Menu Stack` → Frontend.WidgetStack.GameMenu
* `Game HUD Stack` → Frontend.WidgetStack.HUD
* `Front end Stack` → Frontend.WidgetStack.Frontend