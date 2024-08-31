---
title: Custom Niagara Module
date: 2024-05-08
categories: [unreal, Niagara]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Custom Niagara Module

* Custom Niagara Module 을 만드는 방법은 2가지가 있다.

* 가장 빠른 방법은 Niagara 안에서 `새 스크래치 패치 모듈(New Scratch Pad Module)`을 만드는 것이다

  * 사진에는 파티클 스폰에서 생성되었지만, 다른 곳에서도 생성이 가능

<center><img src="./../../../assets/img/Unreal/Niagara/Custom Niagara Module/New Scratch Pad Module.png"></center>


* 스크래치 패치 모듈을 만들면 블루프린트와 매우 비슷하게 그래프 노드 형식을 사용한다

* 차이점은 블루프린트에서는 C++를 사용하지만 스크래치는 HLSL를 이용한다

* Scratch를 만들면 Parameter과 Map이 있다.

<br>

## Parameter

* Parameter에는 C++처럼 `NameSpace`들로 구분되어 있다.

* NameSpace의 종류는 아래와 같이 있다.

```
- SYSTEM :  나이아가라 시스템의 전반적인 상태를 관리하며, 모든 구성 요소에 영향 미침
(모든 곳에서 읽을 수 있음)

- EMITTER : . 각 발사체 내부에서만 접근할 수 있는 변수들을 포함하며, 다른 발사체에는 영향을 미치지 않음 (Emitter와 particle stages에서 읽을 수 있음)

- PARTICLES : 각 파티클에 대해 독립적으로 설정되는 변수들이며, 이 변수들은 다른 파티클에 영향을 미치지 않음 (Paritcle stages에서 읽을 수 있음)

- INPUT : 외부 입력에 의해 설정되는 변수 (전역에서 접근 가능)

- OUTPUT : 외부로 출력되거나 다른 시스템에 전달될 변수로 (전역에서 접근 가능)

- STACKCONTEXT : 모듈 스택 내에서 중간 연산 또는 상태 관리에 사용되며, 그 외의 시스템 요소에서는 접근불가(주로 임시적인 계산이나 상태 저장에 활용)

- TRANSIENT : 일시적인 데이터나 상태를 저장하는 데 사용되며, 연산이 끝난 후 사라짐
(일반적으로 특정 스크립트나 연산 단위에서만 유효)
```

* SYSTEM과 EMITTER에 둘 다 Age라는 변수가 있는데 이를 구분하기 위해서는 `NameSpace.변수명` 형식으로 사용한다

  * `System.Age`, `Emitter.Age`

* 그래서 이러한 값들은 Niagara의 모듈들에 의해 값을 수정하는 형태로 있다.

* Niagara의 모듈을 클릭하고 우측 상단에서 `파라미터 읽기/쓰기 표시`를 사용하면 이 모듈이 현재 사용하고 있는 Parameter 값들을 알 수 있다.


<center><img src="./../../../assets/img/Unreal/Niagara/Custom Niagara Module/Module Using Parameter.png"></center>


## Map

* Scratch를 생성하고 나면 아래와 같은 창이 뜬다

<center><img src="./../../../assets/img/Unreal/Niagara/Custom Niagara Module/Map GetSet.png"></center>


* 여기서 `Map Get(맵 가져오기)`에서 새로운 Parameter를 만들고 기존 Parameter에 있는 값들을 `Map Set(맵 설정)`수정할 수 있다.

### 예시


* Map Get에서 float 변수 하나를 만들고 네이밍을 한 다음 

<center><img src="./../../../assets/img/Unreal/Niagara/Custom Niagara Module/New Float.png"></center>


* Map Set에서 Parameter를 불러와 그 값으로 적용시킬 수 있다.

<center><img src="./../../../assets/img/Unreal/Niagara/Custom Niagara Module/LifeTime  Float.png"></center>

* 위 사진은 float으로 만든 LifeTime이라는 값을 파티클에 있는 LifeTime으로 덮어씌우겠다는 의미

* 이러고 나서 Niagara로 돌아가서 해당 스크래치를 클릭하면 기존 모듈처럼 Detail에서 입력의 LifeTime의 값을 수정할 수 있다.