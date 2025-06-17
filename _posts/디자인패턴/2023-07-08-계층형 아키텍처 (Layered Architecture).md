---
title: 계층형 아키텍처 (Layered Architecture)
date: 2023-07-08
categories: [디자인 패턴, 아키텍처 패턴]
tags: [design pattern]		# TAG는 반드시 소문자로 이루어져야함!
---

# 계층형 아키텍처 (Layered Architecture)

* 시스템의 구성 요소들을 서로 다른 **계층(Layer)**으로 분리하여 구성하는 가장 보편적인 아키텍처 패턴
* 각 계층은 특정 역할과 책임에만 집중하며, 정해진 규칙에 따라 인접한 계층과만 상호작용한다.

<br>

* 이 패턴의 핵심 목표는 **관심사의 분리(Separation of Concerns)**다. 

* 예를 들어, 화면에 보이는 UI 로직과 실제 게임 규칙 로직, 그리고 데이터를 저장하는 로직을 뒤섞는 대신, 각각을 별개의 층으로 분리하여 코드의 재사용성과 유지보수성을 높이는 것이다.

<br>

* 가장 일반적인 형태는 3계층 아키텍처(3-Tier Architecture)다.

  * `프레젠테이션 계층 (Presentation Layer)`: 사용자에게 보여지는 부분. (UI, 렌더링)

  * `비즈니스 로직 계층 (Business Logic Layer)`: 시스템의 핵심 규칙과 로직. (게임 규칙, 상태 관리)

  * `데이터 접근 계층 (Data Access Layer)`: 데이터의 저장과 조회. (파일 저장/불러오기, 데이터베이스)

## 시나리오: 간단한 RPG 게임의 구조

* 플레이어의 정보를 관리하고, 아이템을 획득하며, 게임을 저장하는 간단한 RPG 게임을 만든다고 가정하자.

### 문제점: 스파게티 코드 (Spaghetti Code)

* 계층 구분이 없다면, 모든 코드가 한 곳에 뒤섞이기 쉽다.

```c++
// 안티 패턴: 모든 로직이 한 클래스에 섞여있음
class Monster {
public:
    void Die() {
        // 게임 규칙 로직
        player.experience += 100;
        player.gold += 50;

        // 프레젠테이션(UI) 로직
        ShowDeathAnimation();
        UpdateQuestUI("몬스터 처치: 1/10");

        // 데이터 접근 로직
        // 파일에 플레이어 데이터를 직접 쓴다.
        ofstream file("savegame.dat");
        file << player.experience << "," << player.gold;
    }
};
```

* 이런 코드는 몬스터가 죽는 로직을 바꾸고 싶을 뿐인데, UI나 파일 저장 방식까지 신경 써야 한다. 테스트는 거의 불가능하며, 코드를 재사용하거나 확장하는 것은 재앙에 가깝다.

## 해결책: 역할에 따른 계층 분리

* 이 문제를 해결하기 위해, 게임 시스템을 3개의 계층으로 명확하게 나눈다.

### 1. 프레젠테이션 계층 (Presentation Layer)

* 이 계층은 오직 보여주고 입력받는 역할만 책임진다. 게임이 내부적으로 어떻게 동작하는지는 전혀 알지 못한다.

```c++
// Presentation Layer
class GameUI {
private:
    GameLogic& game; // 핵심 로직 계층에 대한 참조만 가짐
public:
    GameUI(GameLogic& logic) : game(logic) {}

    void Render() {
        // game 객체로부터 데이터를 '요청'하여 화면에 그린다.
        cout << "플레이어 HP: " << game.GetPlayerHealth() << endl;
    }

    void OnAttackButtonPressed() {
        // 사용자 입력을 핵심 로직 계층으로 '전달'할 뿐이다.
        game.ExecutePlayerAttack();
    }
};
```

* GameUI는 GameLogic을 호출할 수는 있지만, GameLogic이 GameUI를 직접 호출하는 일은 절대 없다.

### 2. 비즈니스 로직 계층 (Game Logic Layer)
* 게임의 모든 규칙과 핵심 로직을 담당한다. 이 계층은 게임의 '두뇌'다. UI가 버튼으로 만들어졌는지, 데이터가 파일에 저장되는지 알 필요가 없다.

```c++
// Business Logic Layer
class GameLogic {
private:
    DataAccess& data; // 데이터 접근 계층에 대한 참조
    Player player;
public:
    GameLogic(DataAccess& data_layer) : data(data_layer) {}

    void ExecutePlayerAttack() {
        // 게임 규칙을 처리한다.
        Monster* target = FindClosestMonster();
        if (target) {
            player.Attack(target);
        }
    }
    
    void SaveGame() {
        // 데이터 저장이 필요할 때 데이터 접근 계층에 '요청'한다.
        data.SavePlayerData(player.GetData());
    }

    void LoadGame() {
        PlayerData loaded_data = data.LoadPlayerData();
        player.ApplyData(loaded_data);
    }
    
    int GetPlayerHealth() const { return player.GetHealth(); }
};
```

* GameLogic은 상위 계층인 Presentation 계층에 대해 전혀 모르며, 하위 계층인 Data Access 계층의 인터페이스에만 의존한다.

### 3. 데이터 접근 계층 (Data Access Layer)

* 이 계층의 유일한 책임은 데이터를 저장하고 불러오는 것이다. 이 데이터가 게임 캐릭터의 정보인지, 설정 값인지조차 알 필요가 없다.

```c++
// Data Access Layer
class DataAccess {
public:
    void SavePlayerData(const PlayerData& data) {
        // 데이터를 파일이나 DB에 쓰는 로직에만 집중한다.
        cout << "데이터를 파일에 저장합니다..." << endl;
        // ofstream file("save.json");
        // file << ConvertToJson(data);
    }

    PlayerData LoadPlayerData() {
        cout << "파일에서 데이터를 불러옵니다..." << endl;
        // ...
        return {};
    }
};
```

### 계층 간 상호작용 규칙
* 이 구조의 핵심 규칙은 의존성의 방향이 단방향으로만 흐른다는 것이다.

  * `Presentation → Game Logic → Data Access`

* 상위 계층은 바로 아래 하위 계층에만 의존할 수 있다. 
* 하위 계층은 절대로 상위 계층에 대해 알거나 참조해서는 안 된다. 이 '엄격한' 규칙 덕분에 각 계층은 독립적인 부품처럼 교체하거나 테스트할 수 있게 된다.
* 예를 들어, 게임 저장 방식을 JSON에서 데이터베이스로 바꾸고 싶다면 DataAccess 계층만 수정하면 되며, 다른 계층은 전혀 영향을 받지 않는다.

# 요약

* 계층형 아키텍처는 시스템을 논리적인 **계층(Layer)**으로 나누어, 각 계층이 특정 책임만 수행하도록 하는 패턴이다.

* 가장 중요한 원칙은 단방향 의존성이다. 상위 계층은 하위 계층에 의존할 수 있지만, 그 반대는 허용되지 않는다.

* 이를 통해 시스템의 각 부분이 독립적으로 개발, 테스트, 수정될 수 있게 되어 코드의 결합도는 낮아지고(Loose Coupling), 응집도는 높아진다(High Cohesion).

### 장점

* `유지보수성`: 특정 계층의 수정이 다른 계층에 미치는 영향을 최소화하여 유지보수가 용이하다.

* `테스트 용이성`: 각 계층을 독립적으로 테스트할 수 있다. 예를 들어, GameLogic을 테스트할 때 실제 DB 대신 가짜 DataAccess 객체를 주입할 수 있다.

* `재사용성 및 확장성`: 각 계층은 독립적인 모듈이므로 다른 프로젝트에서 재사용하기 쉽고, 새로운 기능을 추가할 때도 해당 계층에만 집중하면 된다.

* `분업 용이`: 팀을 나누어 프론트엔드(프레젠테이션) 개발자와 백엔드(로직, 데이터) 개발자가 각자의 계층을 병렬로 개발할 수 있다.

* 대부분의 소프트웨어 시스템은 명시적으로든 암묵적으로든 이 계층형 아키텍처를 기반으로 설계된다. 이는 복잡한 시스템을 체계적으로 구성하는 가장 기본적이고 강력한 방법 중 하나다.