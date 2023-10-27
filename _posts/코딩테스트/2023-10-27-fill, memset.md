---
title: fill, memest
date: 2023-10-26
categories: [코딩테스트,코딩테스트]
tags: [coding test]		# TAG는 반드시 소문자로 이루어져야함!
---

**memset**
=============

```c++
void* memset( void* dest, int value, std::size_t count );
```

* memset은 int 부분을 unsigned char로 변환한다. 즉, 1byte 단위로 메모리를 초기화 한다는 뜻이다.

* 이러한 특징때문에 만약 자료형이 1byte를 넘어가면 값을 자동으로 그 자료형의 크기에 맞추기 때문에<br>
  -1, 0으로만 초기화가 가능하다.

```c++
#include <iostream>
#include <vector>

using namespace std;

int main()
{
    int arr1[3];
    int arr2[5];

	memset(arr1, 0, sizeof(arr1));
	memset(arr2, -1, sizeof(arr2));
 
    for (int i = 0; i < 3; i++)
    {
        cout << arr1[i] << '\n';
    }

    for (int i = 0; i < 5; i++)
    {
        cout << arr2[i] << '\n';
    }
}
```

* 만약 1같은 값으로 초기화한다면 1byte를 4byte로 늘려서 8비트씩 4개 전부 00000001 이 모양이기 때문에 전혀 다른 값이 나와버린다.


<br>

**fill**
===============

```c++
template< class ForwardIt, class T >
void fill( ForwardIt first, ForwardIt last, const T& value );

template< class ForwardIt, class T >
constexpr void fill( ForwardIt first, ForwardIt last, const T& value );

template< class ExecutionPolicy, class ForwardIt, class T >
void fill( ExecutionPolicy&& policy,
           ForwardIt first, ForwardIt last, const T& value );
```

* fill 은 memset과 달리 반복자(Iterator)를 사용하는 자료구조에도 사용이 가능하다.

```c++
#include <iostream>     
#include <algorithm>    
#include <vector>       
 
using namespace std;

int main() {
 
 
    vector<int> myvector(5);
    // 0 0 0 0 0 
 
    std::fill(myvector.begin(), myvector.begin() + 3, 5);
    // 처음부터 3번째 반복자까지 5로 초기화
    // 5 5 5 0 0
}
```

* 하지만 memset이 low level에서 처리하기때문에 fill보단 memset이 좀더 빠르다