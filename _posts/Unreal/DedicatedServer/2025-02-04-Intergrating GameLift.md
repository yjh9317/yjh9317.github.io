---
title: Intergrating GameLift
date: 2025-02-04
categories: [unreal,DedicatedServer]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# GameLift

* GameLift는 `AWS(Amazon Web Services)에서 제공하는 게임 서버 호스팅 서비스`이다.


# Intergration 

* <https://docs.aws.amazon.com/gamelift/latest/developerguide/integration-engines-setup-unreal.html>

* 위는 공식 문서로, Intergrate하는 방법이 담겨져 있는데 밑에 순서대로 하자면 다음과 같다.

## 1. 선행 조건

* 먼저 통합되기 위해서는 다음과 같은 조건을 만족해야 한다.

  * Target.Type == TargetRules.TargetType.Server
  * 게임 프로젝트는 서버 SDK for Amazon GameLift Servers binary를 인식

<br>

## 2. FProcessParameters 선언

* AWS에서 게임 서버 프로세스를 초기화할 때 사용하는 구조체

* 게임 서버의 라이프사이클(예: 게임 세션 시작, 업데이트, 종료 등) 동안 발생하는 이벤트에 대해 개발자가 정의한 콜백 함수들을 지정하는 역할

<br>

## 3. GameLift 초기화

* FServerParameters 구조체로 서버에 필요한 정보를 설정

```c++
void AShooterGameMode::SetServerParameters(FServerParameters& OutServerParameters)
{
 	UE_LOG(LogShooterGameMode, Log, TEXT("Initializing the GameLift Server"));
 	
    // SDK 모듈 불러오기
 	FGameLiftServerSDKModule* GameLiftSdkModule = &FModuleManager::LoadModuleChecked<FGameLiftServerSDKModule>(FName("GameLiftServerSDK"));

 	FServerParameters ServerParameters;
 
    // AuthToken(인증 토큰) 을 멤버 값에 설정
 	if (FParse::Value(FCommandLine::Get(), TEXT("-authtoken="), ServerParameters.m_authToken))
 	{
 		UE_LOG(LogShooterGameMode, Log, TEXT("AUTH_TOKEN: %s"), *ServerParameters.m_authToken)
 	}

    // 인스턴스의 호스트 ID를 설정
 	if (FParse::Value(FCommandLine::Get(), TEXT("-hostid="), ServerParameters.m_hostId))
 	{
 		UE_LOG(LogShooterGameMode, Log, TEXT("HOST_ID: %s"), *ServerParameters.m_hostId)
 	}
 
    // 플리 ID를 설정
    // 플릿 ID는 해당 게임 서버가 속하는 GameLift Anywhere Fleet의 고유 식별자
 	if (FParse::Value(FCommandLine::Get(), TEXT("-fleetid="), ServerParameters.m_fleetId))
 	{
 		UE_LOG(LogShooterGameMode, Log, TEXT("FLEET_ID: %s"), *ServerParameters.m_fleetId)
 	}

    // GameLift 서비스와의 통신에 사용되는 WebSocket 엔드포인트 설정
 	if (FParse::Value(FCommandLine::Get(), TEXT("-websocketurl="), ServerParameters.m_webSocketUrl))
 	{
 		UE_LOG(LogShooterGameMode, Log, TEXT("WEBSOCKET_URL: %s"), *ServerParameters.m_webSocketUrl)
 	}
}
```

<br>

## 4. GameLift 서버를 초기화 및 콜백 함수 설정


```c++
void AShooterGameMode::InitGameLift()
{
    UE_LOG(LogShooterGameMode, Log, TEXT("Initializing the GameLift Server"));


    FGameLiftServerSDKModule* GameLiftSdkModule = &FModuleManager::LoadModuleChecked<FGameLiftServerSDKModule>(FName("GameLiftServerSDK"));

    // GameLift Anywhere Fleet을 위한 서버 파라미터들을 정의
    FServerParameters ServerParameters;

    SetServerParameters(ServerParameters);

    // GameLift SDK 초기화를 수행
    GameLiftSdkModule->InitSDK(ServerParameters);

    // GameLift가 새 게임 세션을 시작할 때 호출
    auto onGameSession = [=](Aws::GameLift::Server::Model::GameSession gameSession)
    {
        FString gameSessionId = FString(gameSession.GetGameSessionId());
        UE_LOG(LogShooterGameMode, Log, TEXT("GameSession Initializing: %s"), *gameSessionId);
        
        // 게임 세션을 활성화
        GameLiftSdkModule->ActivateGameSession();
    };

    ProcessParameters.OnStartGameSession.BindLambda(onGameSession);

    // GameLift가 서버 종료를 요청할 때 호출
    auto onProcessTerminate = [=]()
    {
        UE_LOG(LogShooterGameMode, Log, TEXT("Game Server Process is terminating"));
        // SDK에 프로세스 종료를 알림
        GameLiftSdkModule->ProcessEnding();
    };

    // ProcessParameters의 OnTerminate 콜백에 위 람다 함수를 바인딩합니다.
    ProcessParameters.OnTerminate.BindLambda(onProcessTerminate);

    // 주기적으로 호출되어 서버가 정상 상태인지 확인
    auto onHealthCheck = []()
    {
        UE_LOG(LogShooterGameMode, Log, TEXT("Performing Health Check"));
        return true;
    };

    // ProcessParameters의 OnHealthCheck 콜백에 위 람다 함수를 바인딩합니다.
    ProcessParameters.OnHealthCheck.BindLambda(onHealthCheck);

    int32 Port = FURL::UrlConfig.DefaultPort;
    ParseCommandLinePort(Port);

    ProcessParameters.port = Port;

    TArray<FString> LogFiles;
    LogFiles.Add(TEXT("FPSTemplate/Saved/Logs/FPSTemplate.log"));
    ProcessParameters.logParameters = LogFiles;

    UE_LOG(LogShooterGameMode, Log, TEXT("Calling Process Ready"));

    // ProcessParameters에 설정된 콜백 함수 및 파라미터들이 SDK에 전달
    GameLiftSdkModule->ProcessReady(ProcessParameters);
}

```