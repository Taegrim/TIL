# 객체지향 설계원칙, 디자인패턴
+ 객체지향의 5원칙 SOLID
+ 디자인 패턴 - 데코레이터, 옵저버, 상태, 전략
#
<br><br>


+ ## 객체지향 설계원칙

### 1. SRP : 단일 책임 원칙 (Single Responsibility)
+ 클래스는  단 하나의 기능만 담당해야한다
+ ex) Player 클래스가 전투 로직, UI 렌더링을 동시에 하면 안됨
<br>

### 2. OCP : 개방-폐쇄 원칙 (Open-Closed)
+ 새로운 기능을 추가할 때(open) 기존 코드를 직접 수정(closed)해서는 안 된다
+ ex) 무기를 추가할 때 기존 Attack 함수에서 if-else 구문을 추가하기보단 Weapon을 상속받는 새로운 클래스를 만들어야 함
<br>

### 3. LSP : 리스코프 치환 원칙 (Liskov Substitution)
+ 부모 클래스가 들어갈 자리에 자식 클래스를 넣어도 의도대로 프로그램이 돌아가야 한다
+ ex) Monster 의 부모 클래스 Character에 Attack()이 있다면 비전투NPC는 이를 상속받으면 안됨
<br>

### 4. ISP : 인터페이스 분리 원칙 (Interface Segregation)
+ 자신이 사용하지 않는 함수가 포함된 인터페이스를 상속받지 않도록 인터페이스를 잘게 쪼개야한다
+ ex) Character에 이동, 공격 등을 몰아넣지 말고 Movable, Attackable 등의 클래스로 분리해야 함
<br>

### 5. DIP : 의존 역전 원칙 (Dependency Inversion)
+ 구체화된 클래스에 의존하지 말고 인터페이스에 의존해야 한다
+ ex) Character 클래스가 Sword 라는 구체적인 클래스보단 Weapon* 같은 인터페이스를 들고 있어야 함
<br>


<br><br><br><br>




+ ## 디자인 패턴

### 1. 데코레이터
+ 객체를 감싸는 형태로 기능을 추가한다<br>
+ 내부적인 구현이 연결리스트, 재귀함수와 유사하다
<br>

````
#include <iostream>
#include <string>

// Component: 무기 인터페이스
class Weapon {
public:
    virtual ~Weapon() = default;
    virtual std::string GetDesc() const = 0;
    virtual int GetDamage() const = 0;
};

// ConcreteComponent: 기본 검
class BasicSword : public Weapon {
public:
    std::string GetDesc() const override { return "강철검"; }
    int GetDamage() const override { return 10; }
};

// Decorator: 장식자 베이스
class Enchantment : public Weapon {
protected:
    Weapon* weapon; // 내부에 감싸고 있는 무기
public:
    Enchantment(Weapon* w) : weapon(w) {}

	virtual ~Enchantment() {
		delete weapon;
	}
};

// ConcreteDecorator: 불 인챈트
class FireEnchant : public Enchantment {
public:
    FireEnchant(Weapon* w) : Enchantment(w) {}
    std::string GetDesc() const override { return weapon->GetDesc() + " + 불꽃"; }
    int GetDamage() const override { return weapon->GetDamage() + 5; } // 공격력 +5
};

// ConcreteDecorator: 얼음 인챈트
class IceEnchant : public Enchantment {
public:
    IceEnchant(Weapon* w) : Enchantment(w) {}
    std::string GetDesc() const override { return weapon->GetDesc() + " + 얼음"; }
    int GetDamage() const override { return weapon->GetDamage() + 3; } // 공격력 +5
};
````
````
// 1. 기본 검 생성
Weapon* mySword = new BasicSword(); // 공격력 10

// 2. 불 인챈트 (검을 포장)
mySword = new FireEnchant(mySword); // 공격력 +5

// 3. 얼음 인챈트 (불타는 검을 포장)
mySword = new IceEnchant(mySword); // 공격력 +3

// 4. 삭제 (ice, fire, basic 순으로 삭제됨)
delete mySword;
````
위와 같은 무기 인챈트 방식이 있다고 하면<br>
mysword -> 얼음 인챈트 -> 불 인챈트 -> Basic Sword 와 같이 연결이 된다<br>
따라서 마치 단방향 연결리스트와 매우 유사한 형태가 되므로 중간에 교체가 필요할 경우 해당 패턴을 사용하지 않는것이 좋다
<br><br>


### 2. 옵저버
+ 객체의 상태가 변하면 이를 관찰하는 여러 객체들에게 알림을 보낸다<br>
+ 언리얼 엔진에선 Event Dispatcher가 이러한 방식이다

````
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>

// 1. Observer 인터페이스: 모든 관찰자의 규격
class IObserver {
public:
    virtual ~IObserver() = default; 
    virtual void OnNotify(int hp) = 0;
};

// 2. Subject: 알림을 보내는 주체 (Character)
class Character {
private:
    std::vector<IObserver*> observers;
    int hp = 100;

public:
    // 관찰자 등록
    void Attach(IObserver* obs) {
        if (obs) observers.push_back(obs);
    }

    // 관찰자 해제
    void Detach(IObserver* obs) {
        observers.erase(std::remove(observers.begin(), observers.end(), obs), observers.end());
    }

    // 상태 변경 및 통지
    void SetHP(int newHP) {
        hp = newHP;
        std::cout << "\n[System] 캐릭터 체력이 " << hp << "(으)로 변경되었습니다." << std::endl;
        Notify();
    }

    void Notify() {
        for (auto obs : observers) {
            obs->OnNotify(hp);
        }
    }
};

// 3. ConcreteObserver A: 체력바 UI
class HealthUI : public IObserver {
public:
    void OnNotify(int hp) override {
        std::cout << "[UI] 현재 체력 게이지 업데이트: " << hp << " / 100" << std::endl;
    }
};

// 4. ConcreteObserver B: 사운드 시스템
class SoundSystem : public IObserver {
public:
    void OnNotify(int hp) override {
        if (hp <= 20) {
            std::cout << "[Sound] 경고! 심박수 소리가 빨라집니다: 두근..두근..!" << std::endl;
        }
    }
};
````
Character는 여러 관찰자를 가지고 있다<br>
여기서 체력이 변할 경우 관찰자들에게 알리고 관찰자들은 내부적으로 변화를 처리한다
<br><br>


### 3. 상태
+ 객체의 행동을 상태별 클래스로 분리한다
+ Update에서 조건을 체크하고 Exit, Enter로 상태를 바꾼다
````
class Character;

// State 인터페이스
class CharacterState {
public:
	virtual ~CharacterState() = default;
    virtual void Handle(Character* owner) = 0;
};

// ConcreteState: 평화 모드
class IdleState : public CharacterState {
public:
    void Handle(Character* owner) override {
        std::cout << "마을을 한가롭게 거닙니다..." << std::endl;
    }
};

// ConcreteState: 전투 모드
class BattleState : public CharacterState {
public:
    void Handle(Character* owner) override {
        std::cout << "주변을 경계하며 무기를 고쳐 잡습니다!" << std::endl;
    }
};

// Context: 캐릭터
class Character {
private:
    CharacterState* currentState;
public:
    Character() { currentState = new IdleState(); }
	~Character() { if(currentState) { delete currentState; } }
    void ChangeState(CharacterState* newState) {
        delete currentState;
        currentState = newState;
    }
    void Update() { currentState->Handle(this); }
};
````
스테이트 머신이 이러한 상태패턴을 이용한다<br>
if-else로 직접 체크가 아닌 각 상태를 객체화하여 상태에서 행동을 처리한다
<br><br>

### 4. 전략
+ 클래스로 캡슐화한 행동을 런타임에 교체한다.
+ 언리얼 엔진에선 Movement 컴포넌트의 Walking, Swimming 등이 이에 해당한다
````
// Strategy 인터페이스
class IAttackStrategy {
public:
	virtual ~IAttackStrategy() = default;
    virtual void Attack() = 0;
};

// ConcreteStrategy: 칼 공격
class SwordAttack : public IAttackStrategy {
public:
    void Attack() override { std::cout << "칼로 베기!" << std::endl; }
};

// ConcreteStrategy: 활 공격
class BowAttack : public IAttackStrategy {
public:
    void Attack() override { std::cout << "화살 쏘기!" << std::endl; }
};

// Context: 캐릭터
class Player {
private:
    IAttackStrategy* strategy;
public:
	~Player() { if(strategy) { delete strategy; }}
    void SetAttackMethod(IAttackStrategy* s) { strategy = s; }
    void Attack() { strategy->Attack(); }
};
````
위의 예시에선 플레이어가 무기를 갈아끼우는것을 구현한 예시이다<br>
기능을 교체한다고 볼 수 있다<br>


<br><br><br>

