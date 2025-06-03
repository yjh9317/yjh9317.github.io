---
title: AWS Configure
date: 2025-02-05
categories: [unreal,DedicatedServer]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# AWS CLI

* AWS를 사용하기 위해선 구성(Configure)을 해야하는데 그전에 AWS CLI(Command Line)을 설치 해야한다

  * 맨 아래 링크 AWS CLI를 보고 다운


# AWS Configure

### 1번

* AWS CLI를 설치 했으면 프로그램 실행창에서 cmd를 열고 이 부분을 실행해야 한다.

* AWS access portal에 간 후, AWS accounts에서 `Access Keys`를 누르고 사용할 플랫폼을 누르면 SSO start URL과 SSO region이 나온다.

### AWS CLI

```
$ aws configure sso
SSO session name (Recommended): 이름
SSO start URL [None]: https://my-sso-portal.awsapps.com/start
SSO region [None]: us-east-1
SSO registration scopes [None]: sso:account:access
```

### 2번

* 위 과정을 마치면 특정 코드를 알려주는 사이트가 나오는데 그 코드를 cmd에 넣어주면 다음과 같이 입력하는 칸이 나온다.

  * CLI client default region
    * 사용할 지역 넣기 (ex: us-east-2)
  * CLI client output format
    * json
  * CLI profile name
    * 이름

### 3번

* 위 과정을 마치면 C드라이브에서 .aws/sso/cache에 들어가면 프로필이 저장되어 있는 메모장이 있다.


## aws sso login --profile

* 이제 cmd를 열고 `aws sso login --profile [입력한 profile name]` AWS에 로그인이 가능해진다.


<br>

# 링크

* AWS CLI

  * <https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/getting-started-install.html>

* AWS Configure

  * <https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-configure-sso.html#sso-configure-profile-token-auto-sso>