---
title: Add Velocity
date: 2024-03-30
categories: [unreal, Niagara]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Add Velocity

* 파티클에 속도를 추가하는 속성

* Particle Spawn에서 추가할 수 있다

## Velocity Mode

* `Linear` : X,Y,Z축 값을 조절해서 원하는 방향으로 설정이 가능하다

* `From Point` : 점(Point)을 기준으로 Random 방향으로 퍼져나감

* `In Cone` : 꼬깔(Cone) 모양으로 퍼져나감

### Velocity Speed Scale

* 속력이라고 생각하면 될 듯 하다

### Rotation 

* Rotation Mode로 `Default, Axis Angle, Yaw/Pitch/Roll, Quaternion, Matrx, None` 까지 지원한다.


## Distribution(분포도)

* Shape에 따라 값이 다르지만 대개로 Mode에서 `Random`으로 무작위로 뿌리거나 `Direct`를 고르고 값을 설정해서 원하는 모양으로 할 수 있다

## Transform

* `Scale,Rotation,Offset`등이 있고 실제 Level에서 배치될 때 유용하다.

  * X축이든 Y축이든 바라보고 싶게할 때 Rotation을 이용

