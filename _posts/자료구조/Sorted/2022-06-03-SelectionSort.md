---
title: SelectionSort
date: 2022-06-03
categories: [자료구조, SelectionSort]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---

선택정렬
==============

* 전체 원소 중에서 기준 위치에 맞는 원소를 선택해 자리를 교환하는 방식
* 정렬되지 않는 원소중 가장 작은 원소를 찾은 다음 ,첫째 원소와 자리를 교환한다.
* 시간복잡도는(n²)

<br><br>

선택정렬 Code
=====================

    void swap(int* a, int* b)
    {
        int tmp = *a;
        *a = *b;
        *b = tmp;
    }


    int main()
    {
        srand((unsigned int)time(NULL));
        
        int iArr[MAX];

        for (int i = 0; i < MAX; ++i)
        {
            iArr[i] = rand() % MAX;
        }
        
        // 선택 정렬
        for (int i = 0; i < MAX - 1; ++i)
        {
            int min = i;
            for (int j = i + 1; j < MAX; ++j)
            {
                if (iArr[j] < iArr[min])
                {
                    min = j;
                }
            }
            
            swap(&iArr[i], &iArr[min]);
        }


        for (int i = 0; i < MAX; ++i)
        {
            cout << iArr[i] << endl;
        }
    }