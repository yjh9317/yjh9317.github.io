---
title: Retarteing과 Root Motion 애니메이션
date: 2024-05-15
categories: [unreal,Animation]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Retarteing과 Root Motion 애니메이션

* Root Motion을 사용하는 애니메이션을 Retargeting을 하면 애니메이션은 적용되지만 Root Motion은 전혀 움직이지 않는 현상이 일어났다.

* 수정하는 방법은 체인 매핑에서 Root을 선택한 다음, 디테일에서 `트랜슬레이션 모드`를 `Globally Scaled`로 바꿔주면 된다.