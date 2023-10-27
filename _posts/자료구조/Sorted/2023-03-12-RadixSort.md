---
title: RadixSort
date: 2023-03-12
categories: [자료구조, Sort]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---


기수 정렬
===============

* 기수 정렬
  * 분배 방식의 정렬 방법으로 정렬할 원소의 키값에 해당하는 버킷(bucket)에 원소를 분배하였다가 버킷의 순서대로 원소를 꺼내는 방법을 반복해서 정렬하는 방법

  

* 시간 복잡도

    * 기수 정렬의 수행시간은 정렬할 원소의 수 n, 키값의 자릿수 d, 버킷수를 결정하는 기수 r에 따라 달라진다.

    * 정렬할 원소 n개를 r개의 버킷에 분배하는 작업이 (n+r)이고, 이 작업을 자릿수 d만큼 반복해야하므로 수행할 작업은 d(n+r)이다.

    * 고로 시간 복잡도는 O(d(n+r))이 된다.



* 특징

  * 원소의 키를 표현하는 값의 기수만큼 버킷이 필요하고, 키값의 자릿수만큼 기수 정렬을 반복한다.

  * 기수 정렬은 n개의 원소에 대한 n개의 메모리 이외에 버킷 큐에 대한 메모리 공간이 추가로 필요하다.

<br>

Code
====================

```c++
#include
#include "LinkedQueue.h"
#define RADIX 10        // 정렬할 자료의 키값이 10진수이므로 10으로 정의
#define DIGIT 2         // 정렬할 자료의 키값이 두 자리이므로 2로 정의


void radixsSort(int a[], int n){
    int i,bucket,d,factor = 1;

    // 정렬할 자료의 기수, 즉 RADIX에 따라 10개의 버킷을 큐로 생성
    LQueueType* Q[RADIX];   // 버킷 큐의 헤드 포인터를 포인터 배열로 선언
    for(bucket = 0; bucket < RADIX; bucket++)
        Q[bucket] = createLinkedQueue();

    
    // 키값의 자릿수만큼, 즉 두번 기수 정렬을 반복 수행
    for(d = 0;d < DIGIT; d++){

        // 키값의 1의 자리에 대한 버킷을 찾아 원소를 저장(enQueue)
        for(i = 0;i< n;i++)
            enLQueue(Q[ (a[i] / factor) % RADIX] , a[i]);
        
        // 버킷 0부터 9까지 저장된 원소를 순서대로 꺼내어(deQueue) 배열 a에 저장
        for(bucket = 0; bucket < RADIX; bucket++)
            while(!isLQEmpty(Q[bucket])) a[i++] = deLQueue(Q[bucket]);
        
        // 1의 자리에 대해 기수 정렬이 끝난 현재 상태를 출력
        printf("\n\n %d 단계 : ",d+1 );
        for(i = 0;i< n;i++) printf(" %3d", a[i]);

        // 10의 자리에 대해 기수 정렬을 반복하기 위해 factor을 상위 단위로 수정(1의 자리 -> 10의 자리)
        factor = factor * RADIX;
    }
}
```