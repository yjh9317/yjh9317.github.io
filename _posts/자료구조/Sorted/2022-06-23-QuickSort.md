---
title: QuickSort
date: 2022-06-23
categories: [자료구조, Sort]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---

QuickSort
========================

* 퀵정렬
  * 분할 정복 알고리즘중 하나인 정렬방법
  * 합병정렬과 비슷하게 전체 리스트를 2개의 부분 리스트로 분할하고, 각각의 부분 리스트를 다시 퀵정렬 하는 분할-정복법을 사용한다
  * 합병정렬과는 다르게 비균등하게(피벗을 기준으로) 분할한다.

<br><br>

* 시간 복잡도
  * 평균적으로 O(nlogn)
  * 최악의 경우 (배열이 오름차순이거나 내림차순일 경우) -> O(n²)

<br><br>

* 과정
    1. 리스트에 있는 한 요소를 피벗으로 선택
    2. 피벗보다 작은 요소들은 피벗의 왼쪽으로 옮기고, 큰 요소들은 피벗의 오른쪽으로 옮긴다.
    3. 이 상태에서 피벗의 왼쪽 리스트와 오른쪽 리스트를 다시 퀵정렬한다.
    4. 위의 과정을 부분 리스트가 더이상 분할하지 못할 때까지 반복한다.


<br><br><br>

QuickSort Code
========================

<br>

        #include<iostream>

        void QuickSort(int* arr,int cnt)
        {
            if (cnt < 2)
                return;
            int iPivot = arr[cnt - 1];  //가장 오른쪽을 피벗으로 선택

            int L = 0;          // 가장 왼쪽
            int R = cnt - 2;    // 피벗을 제외한 가장 오른쪽


            // L과 R이 만나기전까지 반복문
            while (1) {

                // 피벗의 왼쪽에 있는 요소가 피벗보다 크기 전까지 증가
                while ((arr[L] < iPivot) && L < R) L++; 

                // 피벗의 오른쪽에 있는 요소가 피벗보다 작기 전까지 감소
                while ((arr[R] > iPivot) && L < R) R--;

                if (L == R)         // L과 R이 만난다면 break
                {
                    break;
                }
                else                // 만나지 않았더라면 L과 R을 서로 교환
                {
                    int tmp = arr[L];
                    arr[L] = arr[R];
                    arr[R] = tmp;
                }
            }

            //왼쪽 리스트 정렬
            if (arr[L] > iPivot)    // 멈춘 요소가 더 크다면
            {
                //피벗과 자리를 교환한 후
                int tmp = arr[L];
                arr[L] = iPivot;
                arr[cnt - 1] = tmp;

                // 0부터 L까지
                QuickSort(arr, L);
            }
            else                    // 멈춘 요소가 더 작다면
            {
                // 0 부터 L+1 까지
                QuickSort(arr, L + 1);
            }

            // 오른쪽 리스트 정렬
            QuickSort(arr + L + 1, cnt - (L + 1));
        }

        int main()
        {
            int arr[10] = { 10,5,9,8,6,1,3,4,2,7 };

            QuickSort(arr, 10);

            for (int i = 0; i < 10; ++i)
            {
                printf("arr[%d] = %d\n",i, arr[i]);
            }
        }