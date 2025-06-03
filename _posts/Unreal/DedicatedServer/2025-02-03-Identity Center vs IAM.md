---
title: Identity Center vs IAM
date: 2025-02-03
categories: [unreal,DedicatedServer]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# IAM(Identity and Access Management) 개념

* `AWS 계정(루트 계정 아래)의 사용자, 그룹, 역할을 생성하고 관리하며, 각 엔티티(사용자, 역할 등)에게 AWS 서비스나 리소스에 대한 접근 권한을 부여하기 위한 서비스`

  * EC2, S3, RDS 등의 자원에 접근하기 위한 사용자/권한을 생성하고 관리

### IAM 특징

* `Own credentials`

  * IAM 사용자는 각 AWS 계정 내에서 생성되며, 각 사용자마다 고유한 사용자 이름, 암호, 액세스 키 등이 발급된다

  * 사용자가 AWS 콘솔에 로그인하거나 API를 호출할 때, 해당 사용자에게 발급된 자격 증명을 사용합니다. 즉, 각 사용자가 자신만의 로그인 정보(자격 증명)를 가지고 있어 개별적으로 관리된다.

* `Assigned unique permissions (개별 권한 할당)`

  * 각 IAM 사용자마다 구체적인 IAM 정책(policy)을 통해 필요한 권한을 개별적으로 설정할 수 있다.


<br>

# Identity Center

* AWS Organizations와 연계하여 여러 AWS 계정(서브 계정)들을 단일한 인증 창구를 통해 통합적으로 로그인 및 권한 관리할 수 있음

  * 여러 계정을 사용하는 대기업, 조직에서 효율적


### Identity Center 특징


* `Centrally managed`

  * Identity Center 사용자는 조직 전체(여러 AWS 계정 포함)에서 중앙에서 관리

  * 조직 내 모든 AWS 계정에 대해 한 곳에서 사용자 계정을 생성, 업데이트, 삭제하거나 권한을 관리할 수 있다.       

* `Single set of credentials (단일 자격 증명)`

  * 사용자는 하나의 자격 증명을 통해 여러 AWS 계정에 로그인할 수 있다

  *  한 번 로그인하면 여러 계정 및 역할에 대한 접근이 가능하므로, 사용자 입장에서 여러 계정마다 별도의 자격 증명을 기억하거나 관리할 필요가 없다.

* `Access multiple AWS accounts (여러 AWS 계정 접근)`

  * Identity Center는 AWS Organizations와 연동되어, 사용자에게 조직 내 여러 AWS 계정에서의 접근을 손쉽게 설정할 수 있게 한다

  * 대규모 조직이나 멀티 계정 환경에서, 각 계정에 대해 개별 사용자 생성 없이 중앙에서 관리된 인증 정보와 역할 할당을 통해 여러 계정을 한꺼번에 접근할 수 있다.

<br>


# 둘의 차이점

* `Management Scope (관리 범위)`

  * IAM은 특정 AWS 계정 내에서 사용자, 그룹, 역할을 생성 및 관리하지만, Identity Center는 AWS Organizations와 연계하여, 여러 AWS 계정을 포괄하는 중앙 집중식 사용자 및 권한 관리 시스템을 제공

* `Federation (연합/페더레이션)`

  * IAM 사용자들은 AWS 계정 내에서 직접 생성되며, 자체 자격 증명을 사용하지만, Identity Center는 사용자가 외부 IdP의 자격 증명을 사용하여 AWS 리소스에 접근할 수 있도록 구성할 수 있다.

* `Single Sign-On (SSO, 단일 로그인)`

  * IAM 사용자는 자신의 별도 자격 증명으로 AWS 계정에 로그인하지만, Identity Center 사용자는 한 번의 로그인으로 조직 내 연결된 모든 AWS 계정과 애플리케이션에 접근할 수 있다.

