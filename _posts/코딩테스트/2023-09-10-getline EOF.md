---
title: getline EOF
date: 2023-09-10
categories: [코딩테스트,코딩테스트]
tags: [coding test]		# TAG는 반드시 소문자로 이루어져야함!
---

* 문제에는 보통 어느 정도 입력을 받을지에 대한 변수가 존재하지만, 없을 때도 있다.

* 그래서 다음과 같이 EOF(End Of File)이 들어오기 전까지 계속 입력받을 수 있는 형태가 있다.

```c++
 while(getline(cin, str)) {
    string tmp;
    cin >> tmp;

    // ...
 }
```

* 기본적으로 EOF가 들어오기 전까지는 계속 입력을 받지만, 윈도우에서는 `Ctrl+Z`를 눌러 EOF를 전달하여 while문이 종료된다.