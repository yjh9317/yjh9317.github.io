---
title: BubbleSort
date: 2022-06-02
categories: [자료구조, BubbleSort]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---

버블정렬
===============
 * 인접한 원소를 두 개 비교하여 자리를 교환하는 방식을 반복하면서 정렬
 * 가장 큰 원소가 마지막 자리에 정렬된다
 * 시간복잡도는(n²)

<br>

버블정렬 Code
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

	    // 버블 정렬
	    for (int i = MAX - 1; i > 0; --i)
	    {
    		for (int j = 0; j < i ; ++j)
		    {
    			if (iArr[j] > iArr[j + 1])
			    {
    				swap(&iArr[j], &iArr[j + 1]);
			    }
		    }	
	    }


	for (int i = 0; i < MAX; ++i)
	{
		cout << iArr[i] << endl;
	}
    }
