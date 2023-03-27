---
title: static과 extern
date: 2023-01-07
categories: [C++,C++]
tags: [C++]		# TAG는 반드시 소문자로 이루어져야함!
---

static 데이터 멤버와 메서드
============
* 클래스의 데이터 멤버와 메서드를 static으로 선언할 수 있다.

* static으로 지정하지 않은 멤버와 달리 객체에 속하지 않는다.

* static 데이터 멤버는 객체 외부에 단 하나만 존재

* static 메서드도 객체가 아닌 클래스에 속하고 특정 객체를 통해 실행되지 않는다.

<br>

static 링크
=================
  
* C++에서 링크를 처리하는법
  * C++는 코드를 소스 파일 단위로 컴파일 해서 그 결과로 나온 오브젝트 파일들을 링크 단계에서 연결
  * 외부 링크로 연결되면 다른 소스 파일에서 이름을 사용할 수 있고,<br> 내부 링크로 연결되면 같은 파일 에서만 사용할 수 있다.

<br>

* 선언문 앞에 static 키워드를 붙이면 내부 링크(정적 링크)가 적용된다.

```c++
FirstFile.cpp
---------------
void f();   // f()를 선언만 하고 정의는 X

int main()
{
    f();
    return 0;
}


AnotherFile.cpp
---------------
#include <iostream>

void f();     // 선언과 정의

void f()
{
    std::cout<<"f\n";
}
```

<br>

* f()가 외부 링크로 처리되어 main()에서 다른 파일에 있는 함수를 호출하기 때문에 에러없이 컴파일되고 링크된다

* 만약 AnotherFile.cpp의 f()의 앞에 static을 붙인다면 컴파일 과정에서 에러는 발생하지 않지만 링크 단계에서 에러가 발생한다.

* static 대신 익명 네임스페이스를 이용하여 내부 링크가 적용되게 할 수도 있다.

```c++
AnotherFile.cpp
---------------
#include <iostream>

static void f();     // static 메서드로, 내부 링크가 적용

void f()
{
    std::cout<<"f\n";
}

/*
namespace{
    void f();     // 네임 스페이스로 인한 내부 링크 적용

    void f()
    {
        std::cout<<"f\n";
    }
}

*/
```

<br>

extern
=============
* static과 반대로 외부 링크를 지정할 때 사용
* const와 typedef는 기본적으로 내부 링크로 처리되지만, extern을 붙이면 외부 링크가 적용된다.

* extern 적용 과정
  * extern으로 지정하면 컴파일러는 이를 정의가 아닌 선언문으로 취급한다
  * 변수를 extern으로 지정하면 컴파일러는 그 변수에 대해 메모리를 할당하지 않는다.
  * 따라서 그 변수를 정의하는 문장은 따로 작성해야 한다.

<br>

* extern이 필요한 경우는 다른 소스 파일의 변수를 접근하게 만들 때이다.

```c++
FirstFile.cpp
===========
int x = 3;
// 기본적으로 전역 변수는 extern 변수로 간주
// 하지만 상수 (const) 전역 변수는 static 변수로 간주 한다


main.cpp
=================
#include <iostream>

extern int x;

int main()
{
    std::cout<< x << std::endl;
    // 3 출력
}
```

<br>

함수 안의 static 변수
======================
* static은 특정한 스코프 안에서만 값을 유지하는 로컬 변수로도 만들 수 있다.
* 함수 안에서 static으로 지정한 변수는 그 함수만 접근할 수 있는 전역 변수
* 주로 함수에서 초기화 작업 수행 여부를 기억하는 용도로 많이 사용



```c++
void Func()
{
    static int i = 0;
    ++i;

    cout << i << endl;
}

int main()
{
    Func();     // 1 출력
    Func();     // static 변수는 첫 초기화만 실행하므로 i가 1인 상태에서 ++i가 실행되어 2 출력
}
```

<br>

함수 링크
=====================
* 함수는 변수와 같이 링크 속성을 가진다.
* 함수는 기본적으로 외부 링크가 기본 설정이지만, static 키워드를 통해 내부 링크로 적용할 수 있다.

```c++
static int add(int x, int y)
{
    return x+y;
}
```