---
title: UPROPERTY
date: 2023-05-12
categories: [unreal,unreal]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

UPROPERTY
==============

* 변수와 같이 사용하는 리플렉션 매크로로, 매크로의 인자에 값을 넣어 여러가지 방면으로 사용할 수 있다.


* UPROPERTY에서 자주 사용할 매크로 위주로 작성하려고 한다.

<br><br>

수정 및 공개
==================

* 에디터나 월드에서 변수를 수정할 수 있게 하거나 어디까지 공개할지를 정하는 프로퍼티

<br>

* Visible과 Edit은 변수의 수정 가능 여부를 의미한다.

  * Visible이 사용되면 그 변수는 Read만 가능하다
  * Edit이 사용되면 그 변수는 Read & Write이 가능하다

<br>

* DefaultsOnly는 , InstanceOnly는, Anywhere은 변수 공개 범위를 의미한다.

    * DefaultsOnly는 블루 프린트의 에디터에서만 공개함을 의미한다

    * InstanceOnly는 월드 상에서 존재하는 오브젝트에서만 공개함을 의미한다.

    * Anywhere은 Defaults와 Instance를 합친 것으로, 에디터와 월드 둘 다 접근 가능하다


* 위를 토대로 작성해보면

```
* Visible

VisibleDefaultsOnly : 블루프린트 에디터 창의 디테일 패널에서 값을 보기 가능

VisibleInstanceOnly : 월드상에 배치된 오브젝트의 디테일 패널에서 값을 보기 가능

VisibleAnywhere : 블루프린트 에디터 & 월드상에 배치된 오브젝트의 디테일 패널에서 값을 보기 가능

* Edit
 
EditDefulatOnly : 블루프린트 에디터 창의 디테일 패널에서 값을 수정 가능

EditInstanceOnly : 월드상에 배치된 오브젝트의 디테일 패널에서 값을 수정 가능

EditAnywhere : 블루프린트 에디터 & 월드상에 배치된 오브젝트의 디테일 패널에서 값을 수정 가능
```

<br><br>

블루 프린트 
===========

* 블루 프린트에서 변수를 수정할 수 있게 하거나 어디까지 공개할지를 정하는 프로퍼티

<br>

* 총 4가지로 아래와 같이 있다

```

* BlueprintReadOnly : 블루프린트에서 해당 변수를 읽기

* BlueprintReadWrite : 블루프린트에서 해당 변수를 읽기 & 쓰기

* BlueprintGetter :	해당 변수에 접근 할 수 있는 함수를 지정하고 블루프린트는 해당 함수를 통해 변수에 접근

* BlueprintSetter :	해당 변수에 수정 할 수 있는 함수를 지정하고 블루프린트는 해당 함수를 통해 변수에 수정
```

<br><br>

카테고리
===============

* 카테고리를 이용해 에디터상에서 변수들의 카테고리를 정해줄 수 있다

* | 기호를 이용하여 상,하위 카테고리로도 나눌수도 있다.

<br><br>

Meta
===================

* 에디터 관련 여러가지 기능을 추가할 수 있다

* 변수가 private으로 되어 있지만 "meta = (AllowPrivateAccess = "true")"명령어를 사용하여 에디터에서 보이게 할 수 있다

* UIMin, UIMax는 에디터에서 조정할 수 있는 숫자 범위를 지정하는 명령어

* meta = (BindWidget)를 이용해 블루프린트에서 제작한 변수와 C++를 연결할 수 있다
  * 단 블루프린트에서의 변수 이름과 C++의 변수이름은 동일해야 한다.