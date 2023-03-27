---
title: Reflection System
date: 2023-03-28
categories: [unreal,unreal]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

Reflection System
====================

* 언리얼 Reflection System은 C++ 클래스의 정보를 런타임에 검색하고 수정할 수 있는 시스템입니다.
* 이를 통해 C++ 클래스를 더 유연하게 사용할 수 있으며, 언리얼 엔진의 많은 기능들에서 Reflection System이 사용됩니다.

<br>

* Reflection System은 UClass, UProperty, UFunction, UEnum, UScriptStruct 등의 클래스들을 사용하여 구현됩니다. 각 클래스는 다음과 같은 역할을 합니다.

<br>

* UClass: C++ 클래스를 나타내는 클래스입니다. UClass는 런타임에 C++ 클래스의 정보를 검색하고 수정할 수 있는 기능을 제공합니다. UClass는 Reflection System의 핵심 역할을 합니다.

* UProperty: C++ 클래스의 멤버 변수를 나타내는 클래스입니다. UProperty는 Reflection System에서 변수의 정보를 저장하고 검색하는 데 사용됩니다. UProperty는 또한 Blueprint에서 변수의 노출을 제어할 수 있는 기능도 제공합니다.

* UFunction: C++ 클래스의 멤버 함수를 나타내는 클래스입니다. UFunction은 Reflection System에서 함수의 정보를 저장하고 검색하는 데 사용됩니다. UFunction은 Blueprint에서 함수의 노출을 제어할 수 있는 기능도 제공합니다.

* UEnum: C++ 클래스의 열거형을 나타내는 클래스입니다. UEnum은 Reflection System에서 열거형의 정보를 저장하고 검색하는 데 사용됩니다.

* UScriptStruct: C++ 구조체를 나타내는 클래스입니다. UScriptStruct는 Reflection System에서 구조체의 정보를 저장하고 검색하는 데 사용됩니다.

* Reflection System은 UClass의 GetPropertyByName, SetPropertyByName, FindFunctionByName, Invoke 등의 함수를 사용하여 C++ 클래스의 정보를 검색하고 수정합니다. Reflection System은 런타임에 클래스의 정보를 수정할 수 있으므로, 디버깅이나 게임 플레이 중에도 클래스의 정보를 동적으로 변경할 수 있습니다.

* Reflection System은 Blueprint System과 함께 작동하여 Blueprint 클래스와 C++ 클래스 간의 상호 작용을 가능하게 합니다. Blueprint에서 C++ 클래스의 변수나 함수를 노출할 수 있으며, C++ 클래스에서 Blueprint에서 노출된 변수나 함수를 사용할 수 있습니다.

* Reflection System은 언리얼 엔진의 다양한 기능에서 사용되며, C++ 클래스의 유연한 사용을 가능하게 합니다. 이를 통해 개발자는 C++ 클래스를 더 쉽게 사용할 수 있으며, 언리얼 엔진의 다양한 기능을 더 효율적으로 활용할 수 있습니다.