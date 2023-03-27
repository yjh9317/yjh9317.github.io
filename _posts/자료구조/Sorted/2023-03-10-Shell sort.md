---
title: ShellSort
date: 2023-03-10
categories: [자료구조, Sort]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---

셀 정렬
============

* 셀 정렬

  * 일정한 간격으로 떨어져 있는 자료들끼리 부분집합을 구성하고, 각 부분집합에 있는 원소에 대해 삽입 정렬을 수행하는 작업을 반복하여 전체 원소를 정렬하는 방법

    * 전체 원소보다 부분집합으로 나누어 삽입 정렬하는 것이 비교 연산과 자리 이동 연산을 줄일 수 있다.

  

<br>

* 시간 복잡도

  * 비교 횟수는 처음 원소 상태와 상관없이 매개변수 h(부분집합을 만드는 기준)에 영향을 받아서 알고리즘 성능을 분석하기 쉽지 않지만, 일반적으로 (n¹˙²⁵)로 측정한다. \[n^1.25]
  
    * 셀 정렬은 삽입 정렬의 시간복잡도(n²)보다 개선된 방법이다.

<br>

* 특징

    * 셀 정렬은 n개 자료의 메모리 공간과 매개변수를 저장할 공간을 사용한다.

Code
================

* 셀 정렬에서 부분집합을 만드는 기준이 되는 간격을 매개변수 h에 저장한 후에 한 단계가 수행될 때마다 h값을 감소시키고 셀 정렬을 순환 호출하는데, 결국 h가 1이 될 때까지 반복하면서 정렬을 완성한다.

* 일반적으로 사용하는 h값은 원소 개수의 1/2을 사용하고, 한 단계를 수행할 때마다 h값을 반으로 감소시키면서 반복 수행한다.

```c++
#include <stdio.h>
void intervalSort(int a[],int begin, int end, int interval)
{
    int i,j,them;
    for(i = begin + interval; i <= end; i = i + interval)
    {
        item = a[i];
        for(j = i -interval; j >= begin && item < a[j]; j = j - interval)
        {
            a[j + interval] = a[j];
        }
        a[j + interval] = item;
    }
}

void ShellSort(int a[], int size)
{
    int i , interval;
    interval = size / 2;
    while(interval >= 1) 
    {
        for(i = 0; i < interval; i++) intervalSort(a, i , size-1, interval);
        printf("\n interval=%d>>", interval);
        for(i = 0; i< size ; i++) printf("%d", a[i]);
        printf("\n");
        interval =interval /2;
    }
}

int main()
{
    int i, list[8] = { 69,10,30,2,16,8,31,22};
    int size = sizeof(list) / sizeof(list[0]); // list 배열의 원소 개수
    printf("\n정렬할 원소 : ");
    for(i = 0; i < size;i++) printf("%3d", list[i]);
    printf("\n\n<<<<<<<<<<<<<<<<<<셀 정렬 수행>>>>>>>>>>>>>>>>>>\n");
    shellSort(list,size);

    getchar(); return 0;
}
```