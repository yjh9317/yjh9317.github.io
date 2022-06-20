---
title: Singleton Class
date: 2022-06-20
categories: [DirectX, DirectX]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---



Singleton Class
===============
Singleton이란 객체의 인스턴스가 오직 1개만 생성되는 뜻이다.  
Singleton으로 만들고자 하는 Class안에 Singleton(Type)매크로를 사용하면 만들 수 있다.  
Singleton을 class로 만든 이유는 프로그램이 끝나고 나서 해제할 때 Mgr Class(매니저 클래스)에서도 해제 순서가 중요하기 때문에  
atexit함수로 Singleton의 해제순서를 정해준다

Singleton Code
=================


     #pragma once
    
    template<typename T>
    class CSingleton
    {
    	typedef void(*DESTORY)(void);
    
    private:
    	static T* m_Inst;
    
    public:
    	static T* GetInst() //객체생성
    	{
    		if (nullptr == m_Inst)
    		{
    			m_Inst = new T;
    		}
    
    		return m_Inst;
    	}
    
    	static void Destroy() // 객체소멸
    	{
    		if (nullptr != m_Inst)
    		{
    			delete m_Inst;
    			m_Inst = nullptr;
    		}
    	}
    
    public:
    	CSingleton()
    	{
    		atexit((DESTORY)CSingleton<T>::Destroy); // 정적 멤버 함수이기 때문에 일반 전역변수로 캐스팅이 가능하다.
        
        // atexit는 메인함수가 끝날 때(프로그램이 정상적으로 종료) 수행해야할 함수를 스택처럼 등록하는 함수이다.
        // 함수포인터를 넣어놓으면 함수포인터가 스택처럼 쌓여서 메인함수가 끝날 때
        // 등록된 함수를 맨 마지막에 등록된 순서부터 해제한다 (Stack구조)
    	}
    
    	virtual ~CSingleton()
    	{
    
    	}
    };
    
    template<typename T>
    T* CSingleton<T>::m_Inst = nullptr; //정적 변수 초기화
    
    ===============================================================================================================
    
    // 매크로
    #define SINGLE(TYPE) friend class CSingleton<TYPE>;\
					 private:\
						TYPE();\
						~TYPE();
