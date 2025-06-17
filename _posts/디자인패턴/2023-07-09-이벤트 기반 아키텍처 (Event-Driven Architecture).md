---
title: 이벤트 기반 아키텍처 (Event-Driven Architecture)
date: 2023-07-09
categories: [디자인 패턴, 아키텍처 패턴]
tags: [design pattern]		# TAG는 반드시 소문자로 이루어져야함!
---

# 이벤트 기반 아키텍처

* 시스템 내에서 발생하는 **이벤트(Event)**의 생산(Production), 감지(Detection), 그리고 **소비(Consumption)**를 중심으로 소프트웨어를 구성하는 아키텍처 패턴

* 이 구조에서 각 컴포넌트들은 서로를 직접 호출하는 대신, 시스템의 상태 변화를 나타내는 '이벤트'를 발생시키거나, 특정 이벤트가 발생하기를 '구독'하고 기다린다. 
* 이 모든 통신은 이벤트 버스(Event Bus) 또는 **메시지 브로커(Message Broker)**라는 중앙 채널을 통해 이루어진다.

* 이 패턴의 핵심 목표는 시스템을 구성하는 컴포넌트들을 **극도로 분리(Decoupling)**하는 것이다. 
* 이벤트를 발생시키는 쪽(Producer)은 누가 그 이벤트를 받아서 처리하는지 전혀 알 필요가 없으며, 이벤트를 처리하는 쪽(Consumer) 또한 누가 그 이벤트를 발생시켰는지 알 필요가 없다.


## 시나리오: 다양한 게임 시스템 연동하기

* 플레이어가 몬스터를 처치했을 때, 다음과 같은 여러 가지 일이 동시에 일어나야 한다고 가정하자.

  * 퀘스트 시스템: '고블린 10마리 처치' 퀘스트의 카운트를 1 증가시킨다.
  * 업적 시스템: '첫 몬스터 사냥' 업적을 달성 처리한다.
  * UI 시스템: 화면의 킬 카운트를 갱신한다.
  * 사운드 시스템: 몬스터의 비명소리와 함께 경험치 획득 효과음을 재생한다.

### 문제점: 모든 것을 아는 거대 객체

* 이벤트 기반 아키텍처가 없다면, 몬스터의 Die() 메서드는 이 모든 시스템을 직접 참조하고 호출해야 한다.

```c++
// 안티 패턴: 몬스터가 너무 많은 책임을 짐
class Monster {
private:
    QuestSystem* quest_system;
    AchievementSystem* achievement_system;
    UISystem* ui_system;
    SoundSystem* sound_system;
public:
    void Die() {
        // ... 죽는 로직 ...

        // 몬스터가 다른 모든 시스템을 직접 호출한다.
        quest_system->UpdateKillCount(this->type);
        achievement_system->CheckFirstKillAchievement();
        ui_system->UpdateKillCounter();
        sound_system->Play("monster_death.wav");

        // 만약 '길드 시스템'에 몬스터 처치 공지가 추가된다면?
        // 이 클래스를 또 수정해야 한다!
    }
};
```
* 이 코드는 Monster 클래스가 퀘스트, 업적, UI, 사운드 등 자신과 직접 관련 없는 수많은 시스템에 대해 알아야 하는 강한 결합 상태를 만든다. 
* 이는 시스템을 수정하거나 확장하기 매우 어려운 구조다.

## 해결책: 이벤트로 시스템들 분리하기
* 이벤트 기반 아키텍처는 이 문제를 이벤트와 이벤트 버스를 통해 해결한다.

### 1. 이벤트(Event) 정의
* 먼저, 시스템 내에서 발생할 수 있는 의미 있는 사건들을 간단한 데이터 구조체로 정의한다

```c++
#include <string>

using namespace std;

// 몬스터가 죽었을 때 발생하는 이벤트
struct MonsterKilledEvent {
    string monster_type;
    int experience_yield;
};

// 플레이어가 아이템을 획득했을 때 발생하는 이벤트
struct ItemPickedUpEvent {
    string item_id;
    int quantity;
};
```

* 이벤트 객체는 오직 사건에 대한 '정보'만 담을 뿐, 어떤 로직도 가지지 않는다.

### 2. 이벤트 버스 (Event Bus) - 중앙 채널
* 모든 이벤트의 발행(Publish)과 구독(Subscribe)을 중개하는 중앙 통로다. 
* 이는 매개자(Mediator) 패턴과 옵저버(Observer) 패턴의 조합으로 구현될 수 있다.

```c++
#include <functional>
#include <map>
#include <vector>

// 간단한 개념의 이벤트 버스
class EventBus {
public:
    // 이벤트 타입별로 구독자(콜백 함수) 목록을 가짐
    map<string, vector<function<void(void*)>>> subscribers;

    // 특정 이벤트에 대한 구독을 신청
    template <typename T_Event>
    void Subscribe(function<void(T_Event*)> callback) {
        subscribers[typeid(T_Event).name()].push_back(
            // 타입을 안전하게 변환하는 래퍼 람다
            [callback](void* event_data){ callback(static_cast<T_Event*>(event_data)); }
        );
    }

    // 이벤트를 발행하여 모든 구독자에게 알림
    template <typename T_Event>
    void Publish(T_Event* event) {
        string type_name = typeid(T_Event).name();
        if (subscribers.count(type_name)) {
            for (auto& callback : subscribers[type_name]) {
                callback(event);
            }
        }
    }
};
```

* 실제 프로덕션 환경에서는 Boost.Signals2나 직접 구현한 더 정교한 타입 안전 시그널/슬롯 시스템을 사용한다.

### 3. 생산자(Producer)와 소비자(Consumer) 구현

* 이제 각 시스템은 다른 시스템을 직접 참조하는 대신, 이벤트 버스와만 상호작용한다.

```c++
// 이벤트 생산자: 몬스터는 죽을 때 이벤트를 발행만 할 뿐, 누가 듣는지는 모른다.
class Monster {
    EventBus& bus;
public:
    Monster(EventBus& bus) : bus(bus) {}
    void Die() {
        // ...
        MonsterKilledEvent event = {"Goblin", 10};
        bus.Publish(&event);
    }
};

// 이벤트 소비자 1: 퀘스트 시스템은 '몬스터 사망' 이벤트를 구독한다.
class QuestSystem {
public:
    QuestSystem(EventBus& bus) {
        bus.Subscribe<MonsterKilledEvent>([this](MonsterKilledEvent* e){
            OnMonsterKilled(e);
        });
    }
    void OnMonsterKilled(MonsterKilledEvent* event) {
        if (event->monster_type == "Goblin") {
            cout << "[퀘스트 시스템] 고블린 처치 횟수 +1" << endl;
        }
    }
};

// 이벤트 소비자 2: 업적 시스템도 같은 이벤트를 구독한다.
class AchievementSystem {
public:
    AchievementSystem(EventBus& bus) {
        bus.Subscribe<MonsterKilledEvent>([this](MonsterKilledEvent* e){
            cout << "[업적 시스템] '첫 사냥' 업적 달성!" << endl;
        });
    }
};
```

### 4. 전체적인 흐름

* Monster가 죽으면 MonsterKilledEvent를 이벤트 버스에 발행한다. 
* 이벤트 버스는 이 이벤트를 구독하고 있던 QuestSystem과 AchievementSystem에 전달하고, 각 시스템은 전달받은 이벤트 데이터를 기반으로 자신만의 로직을 독립적으로 수행한다.

* 모든 컴포넌트가 완벽하게 분리되었다. 
* 나중에 '길드 시스템'이 몬스터 처치 공지를 띄워야 한다면, 그저 MonsterKilledEvent를 구독하는 GuildSystem 클래스 하나만 추가하면 될 뿐, Monster나 다른 시스템의 코드는 전혀 건드릴 필요가 없다.


# 요약

* 이벤트 기반 아키텍처는 이벤트의 흐름을 통해 시스템의 각 부분을 제어하고 통신하는 아키텍처 스타일이다.

* **이벤트 생산자(Producer)**와 **이벤트 소비자(Consumer)**는 **이벤트 버스(Event Bus)**를 통해서만 상호작용하며, 서로의 존재를 전혀 알지 못한다.
이를 통해 시스템 컴포넌트 간의 **결합도를 최소화(Extremely Decoupled)**할 수 있다.

### 장점

* `극도의 유연성과 확장성`: 새로운 기능(소비자)을 추가할 때, 기존 코드를 전혀 수정하지 않고 새로운 구독자를 이벤트 버스에 등록하기만 하면 된다.

* `비동기 처리 용이`: 이벤트를 큐(Queue)에 넣고 나중에 처리하는 방식으로 쉽게 확장할 수 있어, 시스템의 응답성과 처리량을 높일 수 있다. (예: 많은 로그를 한 번에 파일에 쓰는 작업)

* `향상된 복원력`: 특정 소비자 컴포넌트가 실패하더라도, 이벤트를 발행하는 생산자나 다른 소비자에게는 영향을 주지 않는다.

* `독립적인 개발 및 배포`: 각 컴포넌트(마이크로서비스 아키텍처에서 특히 중요)를 독립적으로 개발, 테스트, 배포할 수 있다.

* 이벤트 기반 아키텍처는 복잡한 상호작용을 가진 현대적인 대규모 어플리케이션이나 게임에서, 시스템을 유연하고 확장 가능하게 유지하기 위한 핵심적인 패러다임이다.
