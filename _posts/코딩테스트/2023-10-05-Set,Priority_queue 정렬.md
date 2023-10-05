---
title: Set,Priority_queue 정렬
date: 2023-10-05
categories: [코딩테스트,코딩테스트]
tags: [coding test]		# TAG는 반드시 소문자로 이루어져야함!
---


**Set 기본 형태**
=========

```c++
// C++ 17 이전
template<
    class Key,
    class Compare = std::less<Key>,
    class Allocator = std::allocator<Key>
> class set;
// C++ 17
namespace pmr {
template<
    class Key,
    class Compare = std::less<Key>
> using set = std::set<Key, Compare, std::pmr::polymorphic_allocator<Key>>;
}
```

* Set은 두 번째 인자인 `Compare` 클래스가 비교 함수로 사용되는 인자이며, less (오름차순) 구조체가 기본적으로 설정되어 있다.


* less 구조체는 다음과 같은 operator()를 지원하며, Set 변수에 넣으면 바꾼 연산자로 정렬 기준을 사용할 수 있다.

```c++
// 기본 형태
constexpr bool operator()(const T &lhs, const T &rhs) const 
{
    return lhs < rhs; 
}
```


## Set 정렬 예시

* 백준 1187번 문제(단어 정렬)

* Set의 두 번째 인자로, operator() 연산자를 가진 구조체를 사용함으로써 정렬 순서를 커스텀할 수 있다.

  * 이 때 operator() 연산자의 매개변수는 첫번째 매개변수의 타입과 일치해야 한다.

```c++
#include <iostream>
#include <set>

using namespace std;

struct Cmp {
	bool operator() (const string& a, const string& b) const {
		if (a.size() == b.size())			// 길이가 같으면 
			return a < b;					// 사전순
		else
			return a.size() < b.size();		// 아니면 길이순
	}
};

int main() {
    // operator()를 지원하는 구조체를 인자로 넣어 정렬 기준 사용
	set<string, Cmp> sets;

    int n;
	cin >> n;
	string tmp;

	for (int i = 0; i < n; i++) {
		cin >> tmp;
		sets.insert(tmp);
	}

	for (const string str : sets) {
		cout << str << '\n';
	}

	return 0;
}
```

<br>


**Priority_queue 기본 형태**
=========

```c++
template<
    class T,
    class Container = std::vector<T>,
    class Compare = std::less<typename Container::value_type>
> class priority_queue;
```


* Priority_queue는 T타입 vector를 사용해서 정렬하기 때문에 Compare을 사용하려면 vector\<T>도 같이 선언해줘야 한다.

## Priority_queue 사용 예시

* 기본적으로 오름차순을 지원하는 priority_queue를 내림차순으로 바꾸는 방법

```c++
#include<iostream>
#include<queue>

using namespace std;

int main(void) {
	priority_queue<int, vector<int>, greater<int>> pq;
	pq.push(5);
	pq.push(1);
	pq.push(7);
}
```

<br>

* set과 마찬가지로 less 구조체를 사용하기 때문에 정렬을 커스텀하기 위해선 operator()를 지원하는 구조체를 따로 선언해서 넣어줘야 한다.

```c++
#include <iostream>
#include <queue>

using namespace std;

struct Cmp {
	bool operator() (pair<int,int>& a, pair<int, int>& b){

		if (a.first == b.first)			// first가 같으면
			return a.second > b.second;	// second를 오름차순으로 정렬

		else 
			return a.first < b.first;	// first가 다르면 first를 기준으로 내림차순

	}
};

int main() {

	priority_queue<pair<int, int>, vector<pair<int, int>>, Cmp> pq;

	pq.push(make_pair(1, 7));
	pq.push(make_pair(4, 2));
	pq.push(make_pair(4, 4));
	pq.push(make_pair(6, 8));

	while(!pq.empty())
	{
		pair<int, int> Cur = pq.top();
		cout << Cur.first << " " << Cur.second << '\n';
		pq.pop();
	}

	return 0;
}
/* 첫번째는 오름차순으로 정렬되다가 같다면 두 번째는 내림차순으로 정렬
6 8
4 2
4 4
1 7
*/ 
```