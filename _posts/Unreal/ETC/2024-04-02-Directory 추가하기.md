---
title: Directory 추가하기
date: 2024-04-02
categories: [unreal,Unreal Etc]
tags: [unreal etc]		# TAG는 반드시 소문자로 이루어져야함!
---

* 기본적으로 클래스에서 헤더파일에 추가할 때 폴더가 있다면 `/`를 붙여 파일 경로를 찾아서 헤더파일으로 선언한다.

*  `(프로젝트이름).build.cs`에 경로를 추가하면 해당 경로를 헤더파일로 선언할 때 `/`로 경로를 찾지 않아도 된다.
 

```c++
// 포함 경로 추가 코드
PrivateIncludePaths.Add();

// 예시
// 프로젝트 이름이 MyProject이고 그 이름을 포함 경로에 추가하면 
// MyProject/OtherFile.h 이런식이 아니라 OtherFile.h만 선언해도 작동한다.
PrivateIncludePaths.Add("MyProject");
```