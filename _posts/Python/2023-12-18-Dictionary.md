---
title: Dictionary
date: 2023-12-18
categories: [python,python]
tags: [python]		# TAG는 반드시 소문자로 이루어져야함!
---

# **Dictionary**

* `Key`와 `Value`로 만들어진 자료구조.

* Key와 Value 서로 한 쌍이고, 중괄호로 만들어지며 쉼표로 구분한다.

```py
# 콜론(:) 앞에 있는 것이 Key이고 뒤에 있는 것이 Value
# Key : name , phone
# Value : pey , 01012345678
dic = {"name":"pey","phone":"01012345678"}
```

* Dictionary 객체에게 Key에 해당하는 값을 인덱스로 주면 Key와 한 쌍이었던 Value값을 사용할 수 있다.


```py
print(dic["name"])
# pey
```

<br>

# 추가

* 위에서는 선언과 동시에 초기화를 한 상태이고 Dictionary에 따로 값을 넣을려면 다음과 같이 해야 한다.

* 그리고 Dictionary에 추가되는 순서의 원칙은 없다

```py
# 생성
a = {1: 'a'}
# Key는 2, Value는 b라는 한 쌍을 추가
a[2] = 'b'
```

# 삭제

* 삭제할 때는 `del` 키워드를 사용한다.

```py
# Key값이 1인 쌍을 삭제
del a[1]
```

# 중첩과 List

* Dictionary의 Value값에는 List와 다른 Dictionary를 사용할 수 있다.

* 단, Key값에는 List와 Dictionary는 사용할 수 없다.

```py
# Animal이라는 Key에 {Dog,Cat}이라는 List로 이뤄진 Value
test = {"Animal" : {"Dog","Cat"} }

# Animal이라는 Key에 또 다른 Dictionary인 Value가 들어가 있는 상태
# Mammal이라는 Key값과 {Dog,Cat}이라는 List로 이뤄진 Value
test2 = {"Animal" : {"Mammal" : {"Dog","Cat"} }}
```

# **Dictionary 함수**

## keys

* keys 함수는 객체안의 Key만 모아서 `dict_keys`라는 객체를 리턴한다.

```py
a = {"name": "pey", "phone": "0119993323", "birth": "1118"}

print(a.keys())
# dict_keys('name','phone','birth')
```

* 다음과 같이 사용할 수도 있다.

```py
a = {"name": "pey", "phone": "0119993323", "birth": "1118"}

for k in a.keys():
  print(k)

# name
# phone
# birth
```

* list로 반환하려면 다음과 같이 하면 된다.

```py
list(a.keys())
# ['phone','birth','name']
```

<br>

## values

* Key만 얻는 것 처럼 Value만 얻는 방법도 있다.

* keys 함수와 다르게 `dict_values`라는 객체에 리턴된다.


```py
a.values()
#dict_values(['pey','0119993323','1118'])
```

<br>


## items

* items 함수는 key와 value의 쌍을 tuple로 묶은 값을 `dict_items` 객체로 돌려준다.

```py
a.items()
# dict_items([('name','pey'), ('phone','011993323'), ('birth','1118')])
```

## clear

* clear 함수는 dictionary의 모든 요소를 삭제한다.

* 빈 dictionary는 {}로 표현한다.

```py
a.clear()
# {}
```

## get

* get(x) 함수는 x라는 key에 해당하는 value를 돌려준다.

* 만약 x라는 값이 없다면, 에러가 발생한다.

```py
a.get('name')
# 'pey'
```

## in

* in은 해당 key가 dictionary 안에 있는지 확인하는 함수이다.

* `"Key" in "객체"`와 같은 방식으로 사용한다.

```py
'name' in a
True

'email' in a
False
```