---
title: TSubclassOf && SpawnActor && SpawnActorDeferred
date: 2024-04-15
categories: [unreal, Unreal Etc]
tags: [unreal]		# TAG는 반드시 소문자로 이루어져야함!
---

# TSubclassOf

* `UClass 유형의 안전성을 보장해 주는 템플릿 클래스`

* TSubclassOf를 사용하면 템플릿 클래스를 상속받는 클래스를 지정해줄 수 있다.

```c++
// AActor를 상속받는 모든 클래스를 Detail창에서 지정할 수 있음
UPROPERTY(EditDefaultsOnly)
TSubclassOf<AActor> ActorClass;
```

<br>

# SpawnActor

* `Actor를 World에 배치하는 템플릿 함수`로 `GetWorld()`로 호출할 수 있다.

* C++에서 BP 객체를 생성하기 위해서 TSubclassOf와 같이 사용한다

```c++
// 생성할 class or BP 
UPROPERTY(EditDefaultsOnly)
TSubclassOf<AActor> TestActorClass;

// 저장할 포인터
UPROPERTY(VisibleAnywhere)
AActor* TestActor;
```

```c++
// 사용
FVector ActorLocation = FVector::ZeroVector;
FRotator ActorRotator = FRotator::ZeroRotator;

// 템플릿에는 클래스, 파라미터는 TSubclassOf 클래스, 위치, 회전 순으로 넣어준다
GetWorld()->SpawnActor<AActor>(TestActorClass,ActorLocation,ActorLocation);
```

<br>

# SpawnActorDeferred

* SpawnActorDeferred와 SpawnActor의 다른점은 `FinishSpawning 함수를 호출하는지 안하는지의 차이`이다.

* SpawnActor는 바로 월드에 배치하는 반면에, `SpawnActorDeffered함수는 원하는 오브젝트의 객체를 생성하고 액터의 FinishSpawning함수를 호출 할 때에만 월드에 배치한다.`