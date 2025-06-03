---
title: Dedicated Server 세팅
date: 2025-02-01
categories: [unreal,DedicatedServer]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# 1. 언리얼 소스 코드 다운로드

* 먼저 언리얼 깃허브 사이트에 들어가서 원하는 빌드 버전의 소스 코드를 다운 받은 다음에 `Setup.bat`, `GenerateProjectFiles.bat`을 순서대로 실행해야 Visual studio 파일이 생긴다

<br>

# 2. Server build Target

* 언리얼에서 클라이언트가 아닌 서버에서 사용할 코드만을 컴파일하기 위해선 Server Build Target을 사용해야 한다.

* 여기서 build 파일은 `언리얼 엔진 프로젝트에서 프로젝트의 컴파일, 빌드, 패키징, 배포와 관련된 설정을 관리하는 파일`이다.

* 언리얼 C++ 파일을 생성하고 나면 기본적으로 아래와 같이 클라이언트용 build 파일이 생긴다.

```c++
// FPSTemplate은 프로젝트 이름(예시)
using UnrealBuildTool;
using System.Collections.Generic;

public class FPSTemplateTarget : TargetRules
{
	public FPSTemplateTarget(TargetInfo Target) : base(Target)
	{
		Type = TargetType.Game;
		DefaultBuildSettings = BuildSettingsVersion.V5;
		IncludeOrderVersion = EngineIncludeOrderVersion.Unreal5_4;
		ExtraModuleNames.Add("FPSTemplate");
    }
}
```

* 그런데 서버 버전을 사용하기 위해선 다음과 같이 기본 빌드 파일의 프로젝트이름 뒤에 Server를 붙이고 Type도 Server로 바꿔줘야 한다.

```c++
using UnrealBuildTool;
using System.Collections.Generic;

public class FPSTemplateServerTarget : TargetRules
{
    public FPSTemplateServerTarget(TargetInfo Target) : base(Target)
    {
        Type = TargetType.Server;
        DefaultBuildSettings = BuildSettingsVersion.V5;
        IncludeOrderVersion = EngineIncludeOrderVersion.Unreal5_4;
    }
}
```

* 그리고 visual stuido 기준으론 왼쪽 위에, Rider 기준으론 오른쪽 위에 `solution configuration`을 `Development Server`로 변환해준 다음에 프로젝트를 build 해주면 된다.

  * build 하기전에 Binaries 파일과 Intermediate 파일을 지운 다음 
  .uproject파일에서 `Generate Visual stuido project files`를 해줘야 visual studio가 새로운 설정을 인식할 수 있다.