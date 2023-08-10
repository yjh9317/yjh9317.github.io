---
title: mutex와 lock
date: 2023-03-16
categories: [C++,C++]
tags: [C++]		# TAG는 반드시 소문자로 이루어져야함!
---

상호 배제
=========
* 멀티스레드 프로그램을 작성할 때는 반드시 연산의 순서를 신중하게 결정해야 한다.

    * 스레드에서 공유 데이터를 읽거나 쓰면 문제가 발생할 수 있다.

<br>

* 이러한 문제를 해결하기 위해서는 스레드끼리 데이터를 아예 공유하지 않거나 공유해야 한다면 한 번에 한 스레드만 접근할 수 있도록 동기화 매커니즘을 제공해야 한다.

* 부울값이나 정숫값을 비롯한 스칼라값은 아토믹 연산만으로도 충분히 동기화 할 수 있다.
  * 하지만 `복잡하게 구성된 데이터를 여러 스레드가 동시에 접근할 때는 동기화 매커니즘을 사용해야 한다`.

* mutex와 lock 클래스를 통해 상호 배제 매커니즘을 제공할 수 있다.

<br>

mutex
===============

* 상호 배제를 뜻하는 mutual exclusion의 줄임말이다.

* mutex의 기본 사용법


  * 다른 스레드와 공유하는 (읽기/쓰기용) 메모리를 사용하려면 먼저 mutex 객체에 락을 걸어야 한다.
  <br>다른스레드가 먼저 락을 걸어뒀다면 그 락이 해제되거나 타임아웃으로 지정된 시간이 경과해야 쓸 수 있다.

  * 스레드가 락을 걸었다면 공유 메모리를 마음 껏 쓸 수 있다. 
  <br>물론 공유 데이터를 사용하려는 스레드마다 뮤텍스에 대한 락을 걸고 해제하는 동작을 정확히 구현해야 한다.

  * 공유 메모리에 대한 읽기/쓰기 작업이 끝나면 다른 스레드가 공유 메모리에 대한 락을 걸 수 있도록 락을 해제한다.
  <br>두 개 이상의 스레드가 락을 걸고 있다면 어느 스레드가 먼저 락을 걸어 작업을 진행할지 알 수 없다.

* C++ 표준은 시간 제약이 없는 뮤텍스(non-timed mutex)와 시간 제약이 있는 뮤텍스(timed mutex) 클래스를 제공한다.

<br>

시간 제약이 없는 뮤텍스 클래스
============



* 표준 라이브러리는 다음과 같은 시간 제약이 없는 뮤텍스 클래스를 제공한다.
  * mutex
  * recursive_mutex
  * shared_mutex

<br>

* mutex와 recursive_mutex는 \<mutex\> 헤더파일에 정의돼 있고 shared_mutex는 C++17부터 추가된  헤더파일에 정의돼 있다

<br>

* 각 뮤텍스마다 다음과 같은 메서드를 제공한다.
------------

<br>

* lock()
  * 호출하는 측의 스레드가 락을 완전히 걸 때까지 대기한다(블록).
  * 이 때 대기시간에 제한은 없다.
  * 스레드가 블록되는 시간을 정하려면 시간 제약이 있는 뮤텍스를 사용한다.

* try_lock()
  * 호출하는 측의 스레드가 락을 걸도록 시도한다.
  * 현재 다른 스레드가 락을 걸었다면 호출이 즉시 리턴된다.
  * 락을 걸었다면 try_lock()은 true를 리턴하고 ,그렇지 않으면 false를 리턴한다.

* unlock()
  * 호출하는 측의 스레드가 현재 걸어둔 락을 해제한다.
  * 그러면 다른 스레드가 락을 걸 수 있게 된다.

<br>

std::mutex
==============
* \<mutex\>헤더파일에 정의돼 있다.

* 소유권을 독점하는 기능을 제공하는 표준 뮤텍스 클래스

* 이 뮤텍스는 한 스레드만 가질 수 있다.

* 다른 스레드가 이 뮤텍스를 소유하려면 lock()을 호출하고 대기한다.

* try_lock()을 호출하면 락 걸기에 실패해 곧바로 리턴된다.

* 뮤텍스를 이미 확보한 스레드가 같은 뮤텍스에 대해 lock()이나 try_lock()을 또 호출하면 데드락이 발생한다.

<br><br>

std::recursive_mutex
=================

* \<mutex\>헤더파일에 정의돼 있다.

* mutex와 비슷하지만 recursive_mutex를 확보한 스레드가 동일한 recursive_mutex에 대해 lock() 이나 try_lockk()을 또 다시 호출할 수 있다.

* recursive_mutex에 대한 락을 해제하려면 lock()이나 try_lock()을 호출한 횟수만큼 unlock()을 호출해야 한다.




<br><br>

std::shared_mutex
===============

* \<shared_mutex\>헤더파일에 정의돼 있다.

* 공유 락 소유권 Or 읽기-쓰기 락이란 개념을 구현한 것.

* 스레드는 락에 대한 독점 소유권이나 공유 소유권을 얻는다

* 독점 소유권 Or 쓰기 락은 다른 스레드가 독점 소유권이나 공유 소유권을 가지고 있지 않을 때만 얻을 수 있다.

* 공유 소유권 Or 읽기 락은 다른 스레드가 독점 소유권을 가지고 있지 않거나 공유 소유권만 가지고 있을 때 얻을 수 있다.

* lock(), try_lock(), unlock() 메서드를 제공한다.

    * 이 메서드는 독점 락을 얻거나 해제한다.

* lock_shared(), try_lock_shared(), unlock_shared()와 같은 공유 소유권 관련 메서드도 제공한다.

    * 공유 소유권 버전의 메서드도 기존 메서드와 비슷하지만 획득하고 해제하는 대상이 공유 소유권이라는 점이 다르다.

<br><br>


시간 제약이 있는 뮤텍스 클래스
============

* 표준 라이브러리는 다음과 같은 시간 제약이 있는 뮤텍스 클래스를 제공한다.
  * timed_mutex
  * recursive_timed_mutex
  * shared_timed_mutex



* timed_mutex와 recursive_timed_mutex 는 \<mutex\>헤더파일에 정의돼 있고 shared_timed_mutex는 \<shared_mutex\>헤더파일에 정의돼 있다.

* 3가지 클래스 모두 lock(), try_lock(), unlock() 메서드를 제공한다.

* shared_timed_mutex는 ock_shared(), try_lock_shared(), unlock_shared()도 제공한다.

* 추가로 다음 메서드도 제공한다.

<br>

* try_lock_for(rel_time)
  * 호출하는 측의 스레드는 주어진 상대 시간 동안 락을 획득하려 시도한다.

  * 주어진 타임 아웃 시간 안에 락을 걸 수 없으면 호출은 실패하고 false를 리턴한다.
  * 주어진 타임아웃 시간 안에 락을 걸었다면 호출을 성공하고 true를 리턴한다.
  * 타임 아웃은 std::chrono::duration 타입으로 지정한다.

<br>

* try_lock_until(abs_time)
  * 호출하는 측의 스레드는 인수로 지정한 절대 시간이 시스템 시간과 같거나 초과하기 전까지 락을 걸도록 
  시도한다.그 시간 내에 락을 걸 수 있다면 true를 리턴한다.
  
  * 지정된 시간이 경과하면 이 함수는 더이상 락을 걸려는 시도를 멈추고 false를 리턴한다.
  * 절대 시간은 std::chrono::time_point로 지정한다.

<br>

* shared_timed_mutex는 try_lock_shared_for() 와 try_lock_shared_until()도 제공한다.

<br><br>

lock
===============

* lock 클래스는 RAII 원칙이 적용되는 클래스로서 뮤텍스에 락을 정확히 걸거나 해제하는 작업을 쉽게 처리해준다.

* lock 클래스의 소멸자는 확보했던 뮤텍스를 자동으로 해제시킨다.

* C++ 표준에서는 std::lock_guard, unique_lock, shared_lock, scope_lock의 4가지 타입의 락을 제공한다

  * scoped_lock는 C++17부터 추가됐다.

<br><br>

lock_guard
=====================

* \<mutex\> 헤더파일에 정의돼 있다.

* 다음 두 가지 생성자를 제공한다.
------------------


* exlpicit lock_guard(mutex_type& m);
    
  * 뮤텍스에 대한 레퍼런스를 인수로 받는 생성자. 
  *  이 생성자는 전달된 뮤텍스에 락을 걸려 시도하고, 완전히 락을 걸릴 때까지 블록된다.

<br>

* lock_guard(mutex_type& m, adopt_lock_t);

  * 뮤텍스에 대한 레퍼런스와 std::adopt_lock_t의 인스턴스를 인수로 받는 생성자다. 
  * std::adopt_lock이라는 이름으로 미리 정의된 adopt_lock_t 인스턴스가 제공된다.
  * 이 때 호출하는 측의 스레드는 인수로 지정한 뮤텍스에 대한 락을 이미 건 상태에서 추가로 락을 건다.
  * 락이 제거되면 뮤텍스도 자동으로 해제된다.

<br>

unique_lock
=================
  
* \<mutex\> 헤더파일에 정의돼 있다.

* 락을 선언하고 한참 뒤 실행될 때 락을 걸도록 지연시키는 기능을 제공한다.

* owns_lock()이나 unique_lock에서 제공하는 bool 타입 변환를 사용해 lock이 걸렸는지 확인할 수 있다.

* 다음과 같은 생성자를 제공한다.

  * explicit unique_lock(mutex_type& m);

  * unique_lock(mutex_type& m, defer_lock_t) noexcept;

  * unique_lock(mutex_type& m, try_to_lock_t);

  * unique_lock(mutex_type& m, adopt_lock_t);

  * unique_lock(mutex_type& m, const chrono::time_point\<Clock, Duration\>& abs_time);

  * unqiue_lock(mutex_type& m, const chrono::duration\<Rep, Period\>& rel_time);

<br>

* 다음과 같은 메서드를 제공한다.

  * lock()
  * try_lock()
  * try_lock_for()
  * try_lock_until()
  * unlock()

<br><br>

shared_lock
=============

* \<shared_mutex\> 헤더파일에 정의돼 있다.

* unique_lock과 같은 생성자와 메서드를 제공하고, 내부 공유 뮤텍스에 대해 공유 소유권에 관련된 메서드를 호출한다는 점이 다르다.

* shared_lock 메서드는 lock(), try_lock()을 호출할 때 내부적으로 lock_shared(), try_lock_shared()등 공유 뮤텍스에 대한 메서드를 호출한다.

  * 이렇게 하는 이유는 shared_lock와 unique_lock의 인터페이스를 통일시켜 unique_lock을 사용하던 자리에 그대로 넣을 수 있기 때문

<br><br>

한 번에 여러 개의 락을 동시에 걸기 
===============

* C++는 두 가지 제네릭 락 함수를 제공한다.

* 이 함수는 데드락이 발생할 걱정 없이 여러 개의 뮤텍스 객체를 한 번에 거는데 사용된다.

* lock()는 인수로 지정된 뮤텍스 객체를 데드락 발생 걱정 없이 한꺼번에 락을 건다.
  * 이 때 락을 거는 순서는 알 수 없다.
  * 그 중 어느 하나의 뮤텍스 락에 대해 익셉션이 발생하면 이미 확보한 락에 대해 unlock()을 호출한다.

<br>

    * lock()함수의 프로토 타입
    template <class L1, class L2, class... L3> void lock(L1&, L2&, L3&...);

<br>

* try_lock의 프로토타입도 비슷하지만, 주어진 모든 뮤텍스 객체에 대해 락을 걸 때 try_lcok()을 순차적으로 호출한다.

  * 모든 뮤텍스에 대해 try_lock()이 성공하면 -1을 리턴, 하나라도 실패하면 확보된 락에 대해 unlock()을 호출한다.

<br>

* 예시

  *  먼저 두 뮤텍스 에 대한 락을 생성하고, std::defer_lcok_t 인스턴스를 unqiue_lock의 두 번째 인수로 지정해 그 시간안에 락을 걸지 못하게 한다.
  
<br>

    mutex mut1;
    mutex mut2;

    
    void process()
    {
        unique_lock lock1(mut1, defer_lock);  // C++17
        unique_lock lock2(mut2, defer_lock);  // C++17
        
        lock(lock1,lock2);
        // 락을 걸었다.
    } // 락을 자동으로 해제한다.


<br><br>

scoped_lock
==========

* \<mutex\> 헤더파일에 정의돼 있다.

* lock_guard과 비슷하지만 뮤텍스를 지정하는 인수 개수에 제한이 없다.

* scoped_lock을 사용하면 여러 락을 한번에 거는 코드를 간편하게 작성할 수 있다.

<br>

    mutex mut1;
    mutex mut2;

    
    void process()
    {
        scoped_lock locks(lock1,lock2);
        // 락을 걸었다.
    } // 락을 자동으로 해제한다.
