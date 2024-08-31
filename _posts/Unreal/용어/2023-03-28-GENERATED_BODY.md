---
title: GENERATED_BODY
date: 2023-03-28
categories: [unreal, 용어]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---




# GENERATED_BODY, GENERED_USTRUCT_BODY

* 언리얼 엔진의 Reflection System과 통합되는 데 필요한 기본 코드를 자동으로 생성하는 데 사용

*  이 매크로들은 UHT(Unreal Header Tool)에 의해 처리되어, Reflection System에서 필요한 데이터를 생성하고, 클래스 또는 구조체가 엔진의 다양한 기능과 통합된다

<br>

## GENERATED_BODY

* GENERATED_BODY 매크로는 주로 UCLASS 매크로가 있는 클래스에서 사용되며, 해당 클래스가 언리얼 엔진의 Reflection System과 제대로 통합되도록 기본적인 초기화 코드와 메타데이터를 생성

### 기능

* 클래스 초기화: 해당 클래스에 필요한 기본 생성자와 소멸자를 생성

* 메타데이터 연결: 클래스에 메타데이터를 추가하여, 엔진이 클래스의 속성과 기능을 처리

* Reflection 지원: 클래스가 런타임에 Reflection System에 의해 탐지되고 조작될 수 있도록 설정

<br>

## GENERATED_USTRUCT_BODY

* GENERATED_USTRUCT_BODY 매크로는 USTRUCT 매크로가 있는 C++ 구조체에서 사용되며, 구조체가 언리얼 엔진의 Reflection System과 통합될 수 있도록 기본적인 코드를 생성

* 이 매크로는 구조체의 초기화와 메타데이터 설정을 처리하며, 블루프린트 및 기타 시스템과의 통합을 가능하게 함

## 기능

* 구조체 초기화: 구조체의 기본 생성자와 소멸자를 생성

* 메타데이터 추가: 구조체에 필요한 메타데이터를 추가하여, 엔진이 이를 인식하고 처리

* Reflection 지원: 구조체가 런타임에 Reflection System에 의해 관리될 수 있도록 설정