---
title: Custom Location
date: 2025-02-08
categories: [unreal,DedicatedServer]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Custom Location

* Anywhere Fleet을 생성하기 전에 Custom Location(사용자 지정 위치)를 지정해줘야 Anywhere Fleet을 생성이 가능하다

* 먼저, AWS의 Console Home에 가서 Amazon GameLift Server를 검색해서 들어간다.

* 그다음 왼쪽의 목록에서 Anywhere Fleet을 만드는 과정이니 `Anywhere` 하위 목록의 `위치`에 들어간다.

* 그러고 나면 기본적으로 우상단에 있는 위치가 `시스템 정의`로 정의되어 있는데 그 밑에 있는 `위치 생성(Create Location)` 버튼으로 생성이 가능하다.

  * 삭제도 생성 버튼옆에 있어 지울 Location을 선택하고 삭제가 가능

<br>

# AWS CLI

## 생성

* 밑에 링크에 들어가면 `AWS CLI` 버전으로 다음과 같이 Command line으로도 Custom Location을 생성할 수 있다

### AWS CLI

```
aws gamelift create-location \
    --location-name custom-location-1 --profile UserName
```

* 그러면 다음과 같이 출력된다

  * LocationArn은 `리소스의 고유 식별자`로 생각하면 된다

```
{
    "Location": {
        "LocationName": "custom-location-1",
        "LocationArn": "arn:aws:gamelift:us-east-1:111122223333:location/custom-location-1"
    }
}
```

## 삭제

* 삭제 명령어는 아래와 같이 LocationArn을 이용해서 삭제한다.

  * Amazon GameLift Server의 Anywhere Fleet 하위 목록인 위치에 들어가서 LocationArn을 확인할 수도 있다.

### AWS CLI

```
aws gamelift delete-location
    --location-arn <value>
```

<br>

# 링크

* <https://docs.aws.amazon.com/ko_kr/gamelift/latest/developerguide/fleets-creating-anywhere.html#fleet-anywhere-location>