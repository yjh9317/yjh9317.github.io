---
title: lower_bound && upper_bound
date: 2023-10-27
categories: [코딩테스트,코딩테스트]
tags: [coding test]		# TAG는 반드시 소문자로 이루어져야함!
---

**lower_bound**
================

* 기본 형태

```c++
template< class ForwardIt, class T >
ForwardIt lower_bound( ForwardIt first, ForwardIt last, const T& value );

template< class ForwardIt, class T >
constexpr ForwardIt lower_bound( ForwardIt first, ForwardIt last,
                                 const T& value );

template< class ForwardIt, class T >
constexpr ForwardIt lower_bound( ForwardIt first, ForwardIt last,
                                 const T& value );

template< class ForwardIt, class T, class Compare >
constexpr ForwardIt lower_bound( ForwardIt first, ForwardIt last,
                                 const T& value, Compare comp );
```

* 구현

```c++
// 기본 버전
template <class ForwardIt, class T>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value) {
  ForwardIt it;
  typename std::iterator_traits<ForwardIt>::difference_type count, step;
  count = std::distance(first, last);

  while (count > 0) {
    it = first;
    step = count / 2;
    std::advance(it, step);
    if (*it < value) {
      first = ++it;
      count -= step + 1;
    } else
      count = step;
  }
  return first;
}

// Compare 함수 버전
template<class ForwardIt, class T, class Compare>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value, Compare comp)
{
    ForwardIt it;
    typename std::iterator_traits<ForwardIt>::difference_type count, step;
    count = std::distance(first, last);
 
    while (count > 0)
    {
        it = first;
        step = count / 2;
        std::advance(it, step);
 
        if (comp(*it, value))
        {
            first = ++it;
            count -= step + 1;
        }
        else
            count = step;
    }
 
    return first;
}
```

* lower_bound는 범위 [first, last) 안의 원소들 중에서 value 보다 크거나 같은 첫 번째 원소를 리턴한다. 
  * 만일 그런 원소가 없다면 last 를 리턴한다.

* `lower_bound를 사용하기 전에는 오름차순으로 정렬되어 있어야 한다.`

```c++
#include <iostream>     
#include <algorithm>    
#include <vector>       

using namespace std;

int main() {
    vector<int> vec{ 7,4,1,9,2 };

    sort(vec.begin(), vec.end());
    auto lower = std::lower_bound(vec.begin(), vec.end(), 3); // 4 
    auto lower2 = std::lower_bound(vec.begin(), vec.end(), 1); // 2
    auto lower3 = std::lower_bound(vec.begin(), vec.end(), 10); // vec.end() : 마지막 반복자


    cout << *lower << '\n';
    cout << *lower2 << '\n';
    // cout << *lower3 << '\n'; 에러!

}
```

<br>

**upper_bound**
==============

* 기본 형태

```c++
template< class ForwardIt, class T >
ForwardIt upper_bound( ForwardIt first, ForwardIt last, const T& value );

template< class ForwardIt, class T >
ForwardIt upper_bound( ForwardIt first, ForwardIt last, const T& value );

template< class ForwardIt, class T >
ForwardIt upper_bound( ForwardIt first, ForwardIt last, const T& value );

template< class ForwardIt, class T, class Compare >
constexpr ForwardIt upper_bound( ForwardIt first, ForwardIt last,
                                 const T& value, Compare comp );
```

* 구현

```c++
// 기본 버전
template<class ForwardIt, class T>
ForwardIt upper_bound(ForwardIt first, ForwardIt last, const T& value)
{
    ForwardIt it;
    typename std::iterator_traits<ForwardIt>::difference_type count, step;
    count = std::distance(first, last);
 
    while (count > 0)
    {
        it = first; 
        step = count / 2; 
        std::advance(it, step);
 
        if (!(value < *it))
        {
            first = ++it;
            count -= step + 1;
        } 
        else
            count = step;
    }
 
    return first;
}

// Compare 함수 버전
template<class ForwardIt, class T, class Compare>
ForwardIt upper_bound(ForwardIt first, ForwardIt last, const T& value, Compare comp)
{
    ForwardIt it;
    typename std::iterator_traits<ForwardIt>::difference_type count, step;
    count = std::distance(first, last);
 
    while (count > 0)
    {
        it = first; 
        step = count / 2;
        std::advance(it, step);
 
        if (!comp(value, *it))
        {
            first = ++it;
            count -= step + 1;
        } 
        else
            count = step;
    }
 
    return first;
}
```

* upper_bound는 범위 [first, last) 안의 원소들 중에서 value 보다 큰 첫 번째 원소를 리턴한다. 
  * 만일 그런 원소가 없다면 last 를 리턴한다.

* `upper_bound는 사용하기 전에는 오름차순으로 정렬되어 있어야 한다.`

```c++
#include <iostream>     
#include <algorithm>    
#include <vector>       

using namespace std;

int main() {
    vector<int> vec{ 7,4,1,9,2 };

    sort(vec.begin(), vec.end());
    auto lower = std::upper_bound(vec.begin(), vec.end(), 3); // 4
    auto lower2 = std::upper_bound(vec.begin(), vec.end(), 5); // 7
    auto lower3 = std::upper_bound(vec.begin(), vec.end(), 10); // vec.end() : 마지막 반복자

    cout << *lower << '\n';
    cout << *lower2 << '\n';
    // cout << *lower3 << '\n'; 에러!
}
```

<br>

**응용**
=========

```c++
#include <iostream>     
#include <algorithm>    
#include <vector>       

using namespace std;

int main() {
    vector<int> vec{ 8,1,6,12,4,10,15};

    sort(vec.begin(), vec.end());
    // 1,4,6,8,10,12,15


    cout << "5 이상 10 이하의 갯수 : " << upper_bound(vec.begin(), vec.end(), 10) - lower_bound(vec.begin(), vec.end(), 5);
    // 3 (6,8,10)

}
```