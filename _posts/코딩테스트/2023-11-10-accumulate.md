---
title: accumulate
date: 2023-11-10
categories: [코딩테스트,코딩테스트]
tags: [coding test]		# TAG는 반드시 소문자로 이루어져야함!
---

# accumulate

* `numeric` 헤더파일에서 사용할 수 있는 함수로, vector의 합을 바로 반환한다

```c++
#include <numeric>
vector<int> v;

// 3번째 값은 처음 초기화
int sum = accumulate(v.begin(),v.end(),0);
```
