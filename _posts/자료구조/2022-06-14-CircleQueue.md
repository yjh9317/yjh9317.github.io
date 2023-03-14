---
title: Circle Queue
date: 2022-06-14
categories: [자료구조, Queue]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---


Circle Queue
====================

* 선형 큐의 문제점을 개선한 자료구조
* 선형 큐와 원형 큐의 차이점
  1. 선형 큐는 rear이 배열의 마지막 자리가 되어도 front에서 지운 배열의 자리를 활용하지 못함  
  2. 원형 큐는 rear이 꽉 차있을 때 enQueue를 한다면 (cQ->rear + 1) % cQ_SIZE 로 사이즈가 넘어간다면 가장 앞으로 온다


<br><br>
        
Circle Queue Code
=======================


* CircleQueue.h

<br>

    #pragma once
    #define cQ_SIZE  4

    typedef char element;		// 큐 원소(element)의 자료형을 char로 정의

    typedef struct {
        element queue[cQ_SIZE];	// 1차원 배열 큐 선언
        int front, rear;
    } QueueType;

    QueueType* createCQueue();
    int isCQueueEmpty(QueueType* cQ);
    int isCQueueFull(QueueType* cQ);
    void enCQueue(QueueType* cQ, element item);
    element deCQueue(QueueType* cQ);
    element peekCQ(QueueType* cQ);
    void printCQ(QueueType* cQ);


<br>

*  CircleQueue.cpp

<br>

    #include <stdio.h>
    #include <stdlib.h>
    #include "cQueueS.h"

    QueueType* createCQueue() {
        QueueType* cQ;
        cQ = (QueueType*)malloc(sizeof(QueueType));
        cQ->front = 0;       // front 초깃값 설정
        cQ->rear = 0;        // rear 초깃값 설정
        return cQ;
    }

    // 원형 큐가 공백 상태인지 검사하는 연산
    int isCQueueEmpty(QueueType* cQ) {
        if (cQ->front == cQ->rear) {
            printf(" Circular Queue is empty! ");
            return 1;
        }
        else return 0;
    }

    // 원형 큐가 포화 상태인지 검사하는 연산
    int isCQueueFull(QueueType* cQ) {
        if (((cQ->rear + 1) % cQ_SIZE) == cQ->front) {
            printf("  Circular Queue is full! ");
            return 1;
        }
        else return 0;
    }

    // 원형 큐의 rear에 원소를 삽입하는 연산
    void enCQueue(QueueType* cQ, element item) {
        if (isCQueueFull(cQ))	return;
        else {
            cQ->rear = (cQ->rear + 1) % cQ_SIZE;
            cQ->queue[cQ->rear] = item;
        }
    }

    // 원형 큐의 front에서 원소를 삭제하고 반환하는 연산
    element deCQueue(QueueType* cQ) {
        if (isCQueueEmpty(cQ)) return;
        else {
            cQ->front = (cQ->front + 1) % cQ_SIZE;
            return cQ->queue[cQ->front];
        }
    }

    // 원형 큐의 가장 앞에 있는 원소를 검색하는 연산
    element peekCQ(QueueType* cQ) {
        if (isCQueueEmpty(cQ)) exit(1);
        else return cQ->queue[(cQ->front + 1) % cQ_SIZE];
    }

    // 원형 큐의 원소를 출력하는 연산
    void printCQ(QueueType* cQ) {
        int i, first, last;
        first = (cQ->front + 1) % cQ_SIZE;
        last = (cQ->rear + 1) % cQ_SIZE;
        printf(" Circular Queue : [");
        i = first;
        while (i != last) {
            printf("%3c", cQ->queue[i]);
            i = (i + 1) % cQ_SIZE;
        }
        printf(" ] ");
    }
    
