---
title: InsertSort
date: 2022-06-03
categories: [자료구조, InsertSort]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---

삽입정렬
===========
* 정렬되어 있는 부분집합에 정렬할 새로운 원소의 위치를 찾아 삽입하는 방식
* 아직 정렬하지 않은 원소중 하나를 뽑고 정렬되어 있는 부분집합에서 크기를 비교하여 위치에 맞게 배정
* 시간복잡도는(n²)





삽입정렬 Code
======================
    #define MAX 10

    int main()
    {
        srand((unsigned int)time(NULL));

        int iArr[MAX];

        for (int i = 0; i < MAX; ++i)
        {
            iArr[i] = rand() % MAX;
        }

        // 삽입 정렬
        for (int i = 1; i < MAX; ++i)
        {
            int tmp = iArr[i];
            int j = i;
            while ((j > 0) && (iArr[j - 1] > tmp))
            {
                iArr[j] = iArr[j - 1];
                j = j - 1;
            }
            iArr[j] = tmp;
        }


        for (int i = 0; i < MAX; ++i)
        {
            cout << iArr[i] << endl;
        }
    }