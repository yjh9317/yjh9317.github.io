---
title: vector로 queue 초기화
date: 2023-11-07
categories: [코딩테스트,코딩테스트]
tags: [coding test]		# TAG는 반드시 소문자로 이루어져야함!
---

# vector로 queue 초기화

* 다음과 같이 하면 반복문을 쓰지 않아도 초기화 가능

```c++
vector<int> v;
queue<int> q { {begin(v),end(v)} };
```