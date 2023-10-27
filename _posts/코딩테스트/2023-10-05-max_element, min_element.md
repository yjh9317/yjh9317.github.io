---
title: max_element, min_element
date: 2023-10-05
categories: [코딩테스트,코딩테스트]
tags: [coding test]		# TAG는 반드시 소문자로 이루어져야함!
---


**max_element 기본 형태**
================

* #include\<algorithm>에 들어가있는 함수로, 정렬되어 있지 않은 함수에서 최대값과 최소값을 구할 때 사용한다.

* max_element, min_element 함수의 기본 형태는 다음과 같다.

* <https://en.cppreference.com/w/cpp/algorithm/max_element>

```c++
template< class ForwardIt >
ForwardIt max_element( ForwardIt first, ForwardIt last );

template< class ForwardIt >
constexpr ForwardIt max_element( ForwardIt first, ForwardIt last );

 // throw가 던져지면 ExecutionPolicy는 에러 보고
template< class ExecutionPolicy, class ForwardIt >
ForwardIt max_element( ExecutionPolicy&& policy,ForwardIt first, ForwardIt last );
```

* compare 함수도 들어가 있는 기본 형태

```c++
template< class ForwardIt, class Compare >
ForwardIt max_element( ForwardIt first, ForwardIt last, Compare comp );

template< class ForwardIt, class Compare >
constexpr ForwardIt max_element( ForwardIt first, ForwardIt last,Compare comp );

template< class ExecutionPolicy, class ForwardIt, class Compare >
ForwardIt max_element( ExecutionPolicy&& policy,ForwardIt first, ForwardIt last, Compare comp );
```

* 매개변수 타입에 있는 It은 Iterator로, 결국 Iterator를 지원하는 클래스를 대상으로 최대값을 찾아주는 역할을 할 수 있다.

<br>

**max_element 구현**
==========

* 이 또한 <https://en.cppreference.com/w/cpp/algorithm/max_element>에 나와있음.

```c++
// 기본 버전
template<class ForwardIt>
ForwardIt max_element(ForwardIt first, ForwardIt last)
{
    if (first == last)
        return last;
 
    ForwardIt largest = first;
    ++first;
 
    for (; first != last; ++first)
        if (*largest < *first)
            largest = first;
 
    return largest;
}
```

```c++
// Compare 함수가 들어가 있는 버전
template<class ForwardIt, class Compare>
ForwardIt max_element(ForwardIt first, ForwardIt last, Compare comp)
{
    if (first == last)
        return last;
 
    ForwardIt largest = first;
    ++first;
 
    for (; first != last; ++first)
        if (comp(*largest, *first))
            largest = first;
 
    return largest;
}
```

<br>

**min_element 기본 형태**
=============

```c++
template< class ForwardIt >
ForwardIt min_element( ForwardIt first, ForwardIt last );

template< class ForwardIt >
constexpr ForwardIt min_element( ForwardIt first, ForwardIt last );

 // throw가 던져지면 ExecutionPolicy는 에러 보고
template< class ExecutionPolicy, class ForwardIt >
ForwardIt min_element( ExecutionPolicy&& policy,
                       ForwardIt first, ForwardIt last );
```

* compare 함수도 들어가 있는 기본 형태

```c++
template< class ForwardIt, class Compare >
ForwardIt min_element( ForwardIt first, ForwardIt last, Compare comp );

template< class ForwardIt, class Compare >
constexpr ForwardIt min_element( ForwardIt first, ForwardIt last,
                                 Compare comp );

template< class ExecutionPolicy, class ForwardIt, class Compare >
ForwardIt min_element( ExecutionPolicy&& policy,
                       ForwardIt first, ForwardIt last, Compare comp );
```

* 매개변수 타입에 있는 It은 Iterator로, 결국 Iterator를 지원하는 클래스를 대상으로 최소값을 찾아주는 역할을 할 수 있다.


<br>

**min_element 구현**
=============

```c++
// 기본 버전
template<class ForwardIt>
ForwardIt min_element(ForwardIt first, ForwardIt last)
{
    if (first == last)
        return last;
 
    ForwardIt smallest = first;
    ++first;
 
    for (; first != last; ++first)
        if (*first < *smallest)
            smallest = first;
 
    return smallest;
}
```

```c++
// Compare 함수가 들어가 있는 버전
template<class ForwardIt, class Compare>
ForwardIt min_element(ForwardIt first, ForwardIt last, Compare comp)
{
    if (first == last)
        return last;
 
    ForwardIt smallest = first;
    ++first;
 
    for (; first != last; ++first)
        if (comp(*first, *smallest))
            smallest = first;
 
    return smallest;
}
```


<Br>

**예시**
============

* max_element 함수나 min_element 함수의 반환값이 Iterator이므로 포인터 연산자 *를 사용해서 해당 값에 접근할 수 있다.

* 만약 정렬되어 있다면, 정렬된 기준으로 최대값 최소값을 찾는 것이 더 효율적이다.

```c++
#include <iostream>
#include <algorithm>
#include <array>
#include <vector>
#include <list>

using namespace std;

int main()
{
	array<int, 6> arr = { 1,4,9,22,3,6 };
	vector<int> vec = { 16,9,4,2,6,8 };
	list<int> li = { 4,8,14,1,5,3 };

    // 배열
	cout << *max_element(arr.begin(), arr.end()) << '\n';
	cout << *min_element(arr.begin(), arr.end()) << '\n';

    // 벡터
	cout << *max_element(vec.begin(), vec.end()) << '\n';
	cout << *min_element(vec.begin(), vec.end()) << '\n';

    // 리스트
	cout << *max_element(li.begin(), li.end()) << '\n';
	cout << *min_element(li.begin(), li.end()) << '\n';
}
```