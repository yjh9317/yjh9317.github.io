---
title: Priority Queue
date: 2023-03-12
categories: [STL,stl]
tags: [coding test]		# TAG는 반드시 소문자로 이루어져야함!
---


Priority Queue
==================

특징
--------------
* 우선순위 큐는 보통 힙(heap)이라는 자료구조로 구현
  *  힙은 완전 이진 트리

* 우선순위 큐는 일반적인 큐와 비슷하지만, 요소가 삽입될 때 우선순위에 따라 정렬되며, 가장 높은 우선순위를 가진 요소가 먼저 꺼낸다.

* 모든 정점은 자신의 자식들보다 우선순위가 높다.

* top이 최댓값인 우선순위 큐를 최대 힙, 최솟값인 우선순위 큐를 최소 힙이라 함

* 다른 원소들을 다 넣고 전부 pop하면 정렬된 순서로 원소들이 빠져나오게 되는데, 이걸 사용하는 정렬을 힙 소트(heap sort)


헤더파일
---------------
* #include \<queue\>

성능
-----------

  * 추가 연산 성능은 O(log(N))
  
  * 삭제 연산 성능은 O(log(N))
  
<br><br>

priority_queue의 기본 구조
============

<br>

    template <typename T, typename Container = std::vector<T>,
              typename Compare = std::less<typename Container::value_type>>
    class priority_queue;

<br>

* T는 우선순위 큐에 저장될 요소의 타입

* Container는 요소를 저장할 컨테이너 타입을 나타냅니다. 기본적으로 std::vector가 사용된다.

* Compare는 요소를 비교하는데 사용될 함수 객체 타입을 나타낸다.

* 기본적으로는 std::less를 사용하여 요소를 내림차순으로 정렬한다.

<br><br>

priority_queue의 함수
==============

* push(const T& val): 주어진 요소를 우선순위 큐에 삽입합니다.


* pop(): 우선순위 큐에서 가장 높은 우선순위를 가진 요소를 제거합니다.


* top(): 우선순위 큐에서 가장 높은 우선순위를 가진 요소에 대한 참조를 반환합니다.


* empty(): 우선순위 큐가 비어있는지 여부를 반환합니다.


* size(): 우선순위 큐의 요소 수를 반환합니다.

<br><br>

priority_queue의 예시
==============

<br>

    #include <iostream>
    #include <queue>

    int main() {
        // std::priority_queue를 선언하고 초기화합니다.
        std::priority_queue<int> myQueue;

        // 요소를 추가합니다.
        myQueue.push(3);
        myQueue.push(1);
        myQueue.push(4);
        myQueue.push(1);
        myQueue.push(5);

        // 가장 높은 우선순위를 가진 요소를 출력합니다.
        std::cout << "Top element: " << myQueue.top() << std::endl;

        // 모든 요소를 제거합니다.
        while (!myQueue.empty()) {
            std::cout << myQueue.top() << " ";
            myQueue.pop();
        }
        std::cout << std::endl;

        return 0;
    }