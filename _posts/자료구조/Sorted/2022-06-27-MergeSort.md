---
title: MergeSort
date: 2022-06-27
categories: [자료구조, Sort]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---

병합 정렬
========================
* 여러 개의 정렬된 자료 집합을 병합하여 하나의 정렬된 집합으로 만드는 정렬 방법
* 전체 원소에 대해 수행하지 않고 부분집합으로 분할하고 각 부분집합에 대해서 정렬 작업을 정복한 후에 정렬된 부분집합들을 다시 결합하는 분할정복

* 2개의 정렬된 자료 집합을 결합하여 하나의 집합으로 만드는 병합 방법을 2-way 병합이라 한다.

* 시간복잡도
  * O(nlogn)으로 고정이지만 추가적인 저장 공간을 필요로 한다는 단점이 있다.

* 과정
  1. 자료들을 두 개의 부분집합으로 분할한다.(분할)
  2. 부분집합에 있는 원소를 정렬한다. (정복)
  3. 정렬된 부분집합들을 하나의 집합으로 정렬하여 결합한다.(결합)


MergeSort Code
============================

```c++
#include <stdio.h>
#define MAX 30
extern int size;
int sorted[MAX];				// 원소를 병합하면서 정렬한 상태로 저장할 배열 선언

void merge(int a[], int m, int middle, int n) {
    int i, j, k, t;
    i = m;							// 첫 번째 부분집합의 시작 위치 설정
    j = middle + 1;				// 두 번째 부분집합의 시작 위치 설정
    k = m;						// 배열 sorted에 정렬된 원소를 저장할 위치 설정

    while (i <= middle && j <= n) {
        if (a[i] <= a[j])
            sorted[k++] = a[i++];
        else
            sorted[k++] = a[j++];
    } // while
    
    if (i > middle) 
        for (t = j; t <= n; t++, k++) sorted[k] = a[t];
    else 
        for (t = i; t <= middle; t++, k++)	sorted[k] = a[t];
    
    for (t = m; t <= n; t++) 	a[t] = sorted[t];

    printf("\n 병합 정렬 >> ");
    for (t = 0; t < size; t++) printf("%4d ", a[t]);
}

void mergeSort(int a[], int m, int n) {
    int middle; 
    if (m < n) {
        middle = (m + n) / 2;
        mergeSort(a, m, middle);		// 앞 부분에 대한 분할 작업 수행
        mergeSort(a, middle + 1, n);	// 뒷 부분에 대한 분할 작업 수행
        merge(a, m, middle, n);			// 부분집합에 대하여 정렬과 병합 작업 수행 
    }
}
```