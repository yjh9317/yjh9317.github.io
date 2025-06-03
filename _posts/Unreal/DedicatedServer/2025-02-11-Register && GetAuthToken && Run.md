---
title: Register && Get AuthToken && Run .exe
date: 2025-02-11
categories: [unreal,DedicatedServer]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# Register

* Fleet을 사용하기 위해선 이 컴퓨터를 등록해야 한다.
  * compute name은 사용하고 싶은 이름을 넣으면 된다.
  * fleet-id는 fleet에 들어가서 arn을 복사해서 붙여넣으면 된다.
  * ip는 cmd의 ipconfig를 통해서 IPㅍ4 주소를 통해 알아낼 수 있다.
  * location은 사용할 location 입력

### AWS CLI

```
aws gamelift register-compute \
    --compute-name (사용자 정의 이름) \
    --fleet-id (플릿 arn) \
    --ip-address (ip주소) \
    --location (지역)
```

### 결과

```
{
    "Compute": {
        "FleetId": "fleet-2222bbbb-33cc-44dd-55ee-6666ffff77aa",
        "FleetArn": "arn:aws:gamelift:us-west-2:111122223333:fleet/fleet-2222bbbb-33cc-44dd-55ee-6666ffff77aa",
        "ComputeName": "HardwareAnywhere",
        "ComputeArn": "arn:aws:gamelift:us-west-2:111122223333:compute/HardwareAnywhere",
        "IpAddress": "10.1.2.3",
        "ComputeStatus": "Active",
        "Location": "custom-location-1",
        "CreationTime": "2023-02-23T18:09:26.727000+00:00",
        "GameLiftServiceSdkEndpoint": "wss://us-west-2.api.amazongamelift.com"
    }
}
```

* GameLiftServiceSdkEndpoint는 `어느 AWS region의 GameLift 서비스와 통신해야 하는지를 알려주는 구체적인 네트워크 주소(URL)`

<br>

# GetAuthToken

* AuthToken(인증 토큰)은 `안전하게 통신하기 위해 사용하는 만료 시간이 있는 임시 인증 키`로, `특정 컴퓨팅 리소스`가 `특정 플릿`에 속해있으며 `GameLift 서비스와 통신할 권한이 있음을 증명`한다

  * 간단하게 인증번호라고 생각하면 될 것 같다.

### AWS CLI

```
aws gamelift get-compute-auth-token \
    --fleet-id (플릿 arn) \
    --compute-name (이름)
```

### 결과

```
{
    "FleetId": "fleet-2222bbbb-33cc-44dd-55ee-6666ffff77aa",
    "FleetArn": "arn:aws:gamelift:us-east-1:111122223333:fleet/fleet-2222bbbb-33cc-44dd-55ee-6666ffff77aa",
    "ComputeName": "HardwareAnywhere",
    "ComputeArn": "arn:aws:gamelift:us-east-1:111122223333:compute/HardwareAnywhere",
    "AuthToken": "0c728041-3e84-4aaa-b927-a0fb202684c0",
    "ExpirationTimestamp": "2023-02-23T18:47:54+00:00"
}
```

* `"AuthToken"` 뒤에 있는 식별자를 통해서 이제 접속이 가능하다.

# Run .exe
 
### AWS CLI

```
"Path\YourGameProject.exe" ^
 -log ^
 -authtoken= (위에서 얻은 Authtoken) ^
 -hostid=(이름) -fleetid=(플릿 아이디) ^
 -websocketurl= (GameLiftServiceSdkEndpoint)^
 -port= (사용하고 싶은 포트 번호)
```

* `Path`은 언리얼 프로젝트를 (Server 버전)package하고 나온 폴더 경로를 넣고 뒤에 실행파일 이름을 넣어주면 된다

* `authtoken`은 GetAuthToken하고 나온 AuthToken을 넣어주면 된다

* `hostid`는 register할 때 넣은 compute name를 적고 `fleetid`는 생성한 Fleet의 id를 넣어주면 된다

* `websocketurl`은 register에서 나온 `GameLiftServiceSdkEndpoint`를 넣어주면 된다

* port는 사용하고 싶은 포트번호 사용(1024 ~ 49151)

<br>

# 링크

* Register Compute

  * <https://docs.aws.amazon.com/ko_kr/gamelift/latest/developerguide/fleets-creating-anywhere.html#fleet-anywhere-compute>

  * <https://docs.aws.amazon.com/cli/latest/reference/gamelift/register-compute.html>