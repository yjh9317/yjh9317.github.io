---
title: Game Session && Player Session
date: 2025-02-12
categories: [unreal,DedicatedServer]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# GameSession

* `특정 게임 서버 프로세스에서 실행되는 하나의 게임 플레이 인스턴스`

  * 새로운 게임 플레이(매치, 라운드 등)를 위한 환경을 제공
  * 해당 게임 세션의 설정(맵 이름, 게임 모드, 최대 플레이어 수 등)과 상태(현재 플레이어 수, 세션 ID, 서버 IP/포트 등) 정보를 가짐

### AWS CLI

```
aws gamelift create-game-session ^
 --fleet-id (플릿 ID) ^
 --name (이름) ^
 --maximum-player-session-count (최대 플레이어 수) ^
 --location (지역)
```

* 링크에 있는 주소를 통해 추가하고 싶은 목록은 추가 가능

* 생성하고 나면 fleet의 GameSessions창을 통해 Active 상태가 된걸 확인할 수 있다

# Player Session

* `특정 Game Session 내에서 **개별 플레이어를 위한 예약된 '슬롯' 또는 '연결 상태'`를 나타냄

  * 플레이어가 게임 서버에 성공적으로 연결되었는지 추적하고 관리하는 데 사용
  * 플레이어별 데이터(예: 플레이어 ID, 팀 정보 등)를 가질 수 있음

### AWS CLI

```
aws gamelift create-player-session ^
 --game-session-id (Game Session 아이디) ^
 --player-id (사용할 플레이어 아이디)
```

* 링크에 있는 주소를 통해 추가하고 싶은 목록은 추가 가능

* 생성하고 나면 fleet의 GameSessions창을 통해 맨 뒤에 Player Session이 추가된 것을 확인할 수 있다

<br>

# 링크

* Game Session

  * <https://docs.aws.amazon.com/cli/latest/reference/gamelift/create-game-session.html>

* Player Session

  * <https://docs.aws.amazon.com/cli/latest/reference/gamelift/create-player-session.html>