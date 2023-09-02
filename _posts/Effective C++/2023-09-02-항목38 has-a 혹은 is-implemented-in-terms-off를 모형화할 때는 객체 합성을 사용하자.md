---
title: 항목38 has-a 혹은 is-implemented-in-terms-off를 모형화할 때는 객체 합성을 사용하자
date: 2023-09-02
categories: [Effective C++,Effective C++]
tags: [effective c++]		# TAG는 반드시 소문자로 이루어져야
---

* `합성은 어떤 타입의 객체들이 그와 다른 타입의 객체들을 포함하고 있을 경우에 성립하는 그 타입들 사이의 관계를 일컫는다.`

* 합성(composition) 말고도 레이어링(layering), 포함(containtment), 통합(aggregation) 혹은 내장(embedding)으로도 사용한다.


* 포함된 객체들을 모아서 이들을 포함한 다른 객체를 합성한다는 뜻이다.

```c++
class Address { ... };

class PhoneNumber { ... };

class Person{

    private:
        string name;
        Address address;
        PhoneNumber voiceNumber;
};
```

* 위와 같이 Person 객체에는 string, Address, PhoneNumber 객체로 이뤄져 있다.

* 항목32에서 public 상속은 is-a라고 했는데, 객체 합성도 `has-a(...는 ...를 가짐)`과<br> `is-implemented-in-terms-of(...는...를 써서 구현)`을 의미한다.

* 뜻이 두 개인 이유는 소프트웨어 개발에 영역이 두가지 영역(domain)이기 때문인데 하나는 `사물을 본뜬 것들을 소프트 웨어의 응용 능력`이라 하고 `버퍼,뮤텍스,트리 등 시스템구현을 위한 종류는 소프트웨어의 구현 능력`이라고 한다.

* 여기서 `객체 합성이 응용 객체들 사이에서 일어나면 has-a 관계`이고 <br>
  반면, `구현 영역에서 일어나면 객체 합성의 의미는 is-implemented-in-terms-of`이다.

<br>

is-a와 is-implemented-in-terms-of 관계
-------------------------

* 중복 원소가 없는 집합체를 나타내고 저장 공간도 적게 차지하는 클래스 템플릿이 필요하다고 가정한다.

* 기존에 있는 표준 라이브러리인 set을 이용하여 구현하는데 set은 균형 탐색 트리로 구현되어 있어 원소 한 개당 포인터 3개의 오버헤드가 걸린다.

* 그래서 다른 집합 클래스 템플릿인 연결 리스트(list)를 사용하여 재사용하려 한다.

* Set 템플릿을 만들되 list에서 파생된 형태로 시작하도록 만들어 Set 객체는 list의 일종으로 만든다.

```c++
// is-a
template<typename T>
class Set : public list<T> { ... };
```

* 하지만 이런식으로 만들면 같은 값을 중복으로 삽입하면 list는 사본을 두 개를 생성하여 중복 원소가 생긴다.

* 결국 이 두 클래스 사이의 관계가 is-a가 될 수 없으므로 public 상속으로 지금의 관계를 만드는데 적합하지 않다.

* 정답은 Set 객체는 list 객체를 써서 구현되는(is-implemented-in-terms-of) 형태의 설계로 만들어야 한다.

```c++
// is-implemented-in-terms-of
template<class T>
class Set {

    public:
        bool member(const T& item) const;
        void insert(const T& item) const;
        void remove(const T& item) const;

        size_t size() const;
    
    private:
        list<T> rep;        // Set 데이터의 내부 표현부
};
```

* Set 멤버 함수는 list에서 제공하는 기능 및 표준 C++ 라이브러리의 다른 구성 요소를 잘 버무려 만들기만 하면 되기 때문에, 실제 구현은 간단하다.

```c++
template<typename T>
bool Set<T>::member(const T& item) const
{
    return find(rep.begin(), rep.end(), item) != rep.end();
}

template<typename T>
void Set<T>::insert(const T& item) const
{
    if(!member(item)) rep.push_back(item);
}

template<typename T>
void Set<T>::remove(const T& item) const
{
    typename list<T>::iterator it = 
        find(rep.begin(), rep.end(), item);

    if(it != rep.end()) rep.erase(it);
}

template<typename T>
size_t Set<T>::size() const
{
    return rep.size();
}
``` 

<br>

**결론**
=========

> 이것만은 잊지 말자!
> * 객체 합성의 의미는 public 상속이 가진 의미와 완전히 다르다.
> * 응용 영역에서 객체 합성의 의미는 has-a 의미이고, <br>
>   구현 영역에서 객체 합성의 의미는 is-implemented-in-terms-of의 의미이다.
{: .prompt-tip }