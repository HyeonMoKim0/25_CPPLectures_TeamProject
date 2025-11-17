해당 코드 작성자: 임수원

다음은 단순 팩토리 예제, 선언되지 않은 클래스는 무시.
```C++
class WeaponFactory {
public:
    static Weapon* CreateWeapon(int type) {
        if (type == 1) return new Sword();
        if (type == 2) return new Bow();
        if (type == 3) return new Staff();
        return nullptr;
    }
};
```
- ﻿객체 생성 전용 클래스를 따로 두고 new를 사용하지 않도록 하는가장 기초적인 생성 패턴
- If/switch로 어떤 객체를 생성할지 결정
- 장점: ﻿객체 생성 로직이 한 곳에 모여, 쉬운 코드 관리
- 단점: ﻿새로 멤버 변수를 추가할 때마다 팩토리 내부에 switch/if문을 수정 필요, 제품 종류가 많아지면 팩토리가 비대해짐

---

다음은 팩토리 메서드 예제, 선언되지 않은 클래스는 무시.
```C++
class Monster {
public:
    virtual void Attack() = 0;
};

class Slime : public Monster {};
class Orc : public Monster {};

class MonsterSpawner {
public:
    virtual Monster* Spawn() = 0;   // 팩토리 메서드
};

class SlimeSpawner : public MonsterSpawner {
public:
    Monster* Spawn() override { return new Slime(); }
};

class OrcSpawner : public MonsterSpawner {
public:
    Monster* Spawn() override { return new Orc(); }
};
```
- ﻿객체 생성 책임을 서브클래스에게 위임하는 패턴
- 서브클래스가 필요한 객체를 만드는 메서드 구현
- 장점: ﻿새로운 클래스가 추가될 때 기존 코드에 수정이 없고, 상속 기반 확장인 유연한 구조
- 단점: ﻿클래스 수 증가, 단순 팩토리보다 복잡한 구조

---
다음은 추상 팩토리 패턴 예제, 선언되지 않은 클래스는 무시.
```C++
class Warrior {};
class Archer {};
class Mage {};

class AbstractFactory {
public:
    virtual Warrior* CreateWarrior() = 0;
    virtual Archer* CreateArcher() = 0;
    virtual Mage* CreateMage() = 0;
};

class ElfFactory : public AbstractFactory {
public:
    Warrior* CreateWarrior() override { return new ElfWarrior(); }
    Archer* CreateArcher() override { return new ElfArcher(); }
    Mage* CreateMage() override { return new ElfMage(); }
};

class OrcFactory : public AbstractFactory {
public:
    Warrior* CreateWarrior() override { return new OrcWarrior(); }
    Archer* CreateArcher() override { return new OrcArcher(); }
    Mage* CreateMage() override { return new OrcMage(); }
};
```
- ﻿관련된 객체들을 제품군 단위로 묶어 생성하는 패턴
- ﻿장점 : 제품군 단위로 객체 일관성 보장 (엘프 팩토리에서 오크 궁수 같은 것을 생성을 막는다는 얘기)
- 단점 : 가장 복잡한 구조의 팩토리 패턴



