---
title: LinkedList
date: 2022-05-31
categories: [자료구조, LinkedList]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---

<br>

LinkedList
=======================


 * LinkedList
   * 선형 리스트중 하나로 노드가 데이터와 포인터를 가지고 한 줄로 연결되어 있는 방식으로 데이터를 저장하는 자료 구조
   * 구조체(노드)와 포인터를 이용하여 각 구조체를 연결하여 사용


<br><br>

함수
==============

 * init
   * Head와 Tail을 초기화하고 Count를 하나 증가

<br><br>

 * push_back
   * List가 비어있다면 추가한 노드가 Head이자 Tail
   * List가 비어있지 않다면 TailNode뒤에 연결하고 연결한 노드를 TailNode로 설정

<br><br>

 * push_front
   * List가 비어있다면 추가한 노드가 Head이자 Tail
   * List가 비어있지 않다면 먼저 새로운 노드에 Head를 붙이고 그후 연결한 노드를 HeadNode로 설정
  <br><br>
 * pop_back
   * List가 비어있다면 assert
   * List가 하나였다면 그 노드(Head이자Tail)를 메모리 해제(free)
   * Head부터 TailNode의 이전노드까지 탐색하고 TailNode를 찾아내서 메모리 해제한후 이전노드의 다음노드를 가리키는 포인터에 nullptr

<br><br>

 * pop_front
   * List가 비어있다면 assert
   * List가 하나였다면 그 노드(Head이자Tail)를 메모리 해제(free)
   * 삭제하기 전에 삭제할 노드(맨앞)의 다음 노드를 Head로 바꾸고 맨앞 노드를 삭제

<br><br>

 * release
   * 모든 노드 메모리 해제

<br><br>

 * show
   * 모든 노드의 데이터 출력


<br><br>

LinkedList.h
======================

<br>

    #pragma once

    struct INode 
    {
	    int iData;				// int 데이터를 받는 변수
	    INode* NextNode;		// 다음 노드를 연결해줄 변수
    };

    struct IList
    {
	    INode* HeadNode;		// List의 첫노드를 가르키는 변수
	    INode* TailNode;		// List의 마지막노드를 가르키는 변수
	    int CurCount;			// 현재 데이터 갯수
    };

    void Init(IList* _List);
    void push_back(IList* _List,int _iData); // 가장 뒤에 노드를 추가하는 함수
    void push_front(IList* _List, int _iData); // 가장 앞에 노드를 추가하는 함수

    void pop_back(IList* _List); //가장 뒤의 노드를 삭제하는 함수
    void pop_front(IList* _List); //가장 앞의 노드를 삭제하는 함수

    void release(IList* _List); //메모리 해제
    void show(IList* _List);


<br><br>

LinkedList.cpp
============================



	void Init(IList* _List)
	{
		_List->CurCount = 0;
		_List->HeadNode = nullptr;
		_List->TailNode = nullptr;
	}



	void push_back(IList* _List, int _iData)
	{
		// 연결할 노드 동적할당하고 초기화
		INode* NewNode =(INode*)malloc(sizeof(INode));
		NewNode->iData = _iData;
		NewNode->NextNode = nullptr;


		if (nullptr == _List->HeadNode)				// 만약 List안이 비어있다면
		{
			_List->HeadNode = NewNode;				
			_List->TailNode = NewNode;
		}
		else
		{
			//TailNode의 뒤에 Newnode를 연결
			_List->TailNode->NextNode = NewNode;
			//TailNode 갱신
			_List->TailNode = NewNode;
		}

		++_List->CurCount; // 데이터가 추가됐으므로 현재 데이터 개수 하나증가
	}





	void push_front(IList* _List, int _iData)
	{
		INode* NewNode = (INode*)malloc(sizeof(INode));
		NewNode->iData = _iData;

		if (nullptr == _List->HeadNode)
		{
			_List->HeadNode = NewNode;
			_List->TailNode = NewNode;
		}
		else
		{
			// HeadNode에 NewNode를 먼저 연결할 경우 기존에 있었던 HeadNode->NextNode의 연결점이    사라지므로
			// 먼저 NewNode의 Nextnode에 기존에 있는 HeadNode->NextNode의 연결을 먼저해준다 
			NewNode->NextNode = _List->HeadNode;
			_List->HeadNode = NewNode;
		}
		++_List->CurCount;
	}



	void pop_back(IList* _List)
	{
		// 만약 List가 비어있다면 _List->HeadNode가 nullptr이므로 에러발생
		assert(_List->HeadNode);


		//이전노드의 nextnode를 nullptr까지 만들어주어야하므로 이전노드를 구함.
		//삭제할 노드의 이전노드 카운팅
		int Prev = _List->CurCount - 1;
		
		// Prev가 0이면 데이터가 하나라는 뜻
		if (Prev==0)
		{
			free(_List->HeadNode);
			_List->HeadNode = nullptr;
			_List->TailNode = nullptr;
		}
		else
		{
			INode* TmpNode = _List->HeadNode;
			while (1)
			{
				//TailNode의 이전노드까지 이동
				if (TmpNode->NextNode == _List->TailNode)
				{
					break;
				}
				TmpNode = TmpNode->NextNode;
			}
		//기존 TailNode는 free하고 TailNode의 이전노드를 Tailnode로 만들면서 NextNode가     없어졌으므로 nullptr를 넣어준다.
			TmpNode->NextNode = nullptr;
			free(_List->TailNode);
			_List->TailNode = TmpNode;
		}
		--_List->CurCount;
	}



	void pop_front(IList* _List)
	{
		assert(_List->HeadNode); 
		
		int Prev = _List->CurCount - 1;

		if (Prev == 0)
		{
			free(_List->HeadNode);
			_List->HeadNode = nullptr;
			_List->TailNode = nullptr;
		}
		else
		{
			//맨앞의 삭제할 노드를 받는 Node
			INode* Node = _List->HeadNode;

			//삭제하기전에 미리 Head에 삭제할 노드의 다음 노드 연결
			_List->HeadNode = Node->NextNode;

			//삭제하기전에 다음주소를 nullptr
			Node->NextNode = nullptr;

			//삭제할노드를 받은 Node 삭제
			free(Node);
		}
		--_List->CurCount;
	}

	void release(IList* _List)
	{
		INode* Node = _List->HeadNode;
		for (int i = 0; i < _List->CurCount; ++i)
		{
			INode* NextNode = Node->NextNode;
			free(Node);
			Node = NextNode;
		}
	}

	void show(IList* _List)
	{
		INode* Node = _List->HeadNode;
		for (int i = 0; i < _List->CurCount; i++)
		{
			cout << Node->iData << endl;
			Node = Node->NextNode;
		}
	}
