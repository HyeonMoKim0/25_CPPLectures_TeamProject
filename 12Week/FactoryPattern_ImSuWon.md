단순 팩토리 구조에 대해 더 자세히 조사하였음
단순 팩토리(Simple Factory)는 프로그램 내부에서 객체를 생성하는 책임을 한 곳에 모아둔 구조이다.즉, 객체를 직접 new 해서 만드는 대신, 생성 전담 클래스를 통해 객체를 요청하는 방식을 가진다.
이 방식은 다음과 같은 상황에서 특히 유용하다.
- 생성해야 하는 객체 종류가 여러 개일 때
- “어떤 객체를 만들어야 하는지” 결정하는 로직이 자주 바뀔 때
- 클라이언트(사용자 코드)가 구체적인 클래스 이름을 몰라도 객체를 사용할 수 있게 하고 싶을 때

## Client
- 어떤 객체를 만들어야 하는지 Factory에게 부탁한다.

## SimpleFactory
- 객체 생성만 담당하는 “공장 역할”
- 내부에서 if/switch로 어떤 제품을 만들지 결정한다.

## Product(제품)
- 공통 인터페이스(추상 클래스)
## Concrete Product(구체 제품)
- 실제로 만들어지는 클래스들 (Sword/Bow/Staff)

---
#﻿ Product – 모든 무기가 따라야 할 인터페이스
```C++
﻿class Weapon {
public:
    virtual ~Weapon() {}
    virtual void Attack() const = 0;
};
```

#﻿ Concrete Product – 실제 무기들
```C++
﻿class Sword : public Weapon {
public:
    void Attack() const override {
        cout << "[검] 베기 공격!" << endl;
    }
};

class Bow : public Weapon {
public:
    void Attack() const override {
        cout << "[활] 화살 발사!" << endl;
    }
};

class Staff : public Weapon {
public:
    void Attack() const override {
        cout << "[지팡이] 마법 시전!" << endl;
    }
};
```

#﻿ Simple Factory – 생성 전담
```C++
﻿class WeaponFactory {
public:
    static Weapon* CreateWeapon(int type) {
        switch (type) {
        case 1: return new Sword();
        case 2: return new Bow();
        case 3: return new Staff();
        default:
            cout << "존재하지 않는 무기 타입입니다." << endl;
            return nullptr;
        }
    }
};
```

# ﻿Client – 생성 요청만 함
```C++
﻿Weapon* weapon = WeaponFactory::CreateWeapon(userChoice);
weapon->Attack();
delete weapon;
```

﻿1) 생성 코드의 집중화
객체 생성 로직이 공장 클래스에 모여 있어서클라이언트 코드가 훨씬 깔끔해진다.
2) 객체 구조가 변경되어도 클라이언트 변경이 필요 없음
예를 들어, Sword 생성 시 강화된 초기화가 필요하더라도
return new Sword(초기공격력, 강화수치);
이렇게 팩토리 안에서만 수정하면 된다.
3) 인터페이스 기반의 다형성을 적극적으로 활용
클라이언트는 Weapon* 타입만 알고 있으면어떤 무기든 호출할 수 있다 → 확장성 증가

---
﻿6. 단순 팩토리의 한계 (다음 패턴과 연결되는 부분)
단순 팩토리의 가장 큰 한계는 다음과 같다.
제품군이 늘어날수록 Factory가 비대해짐
case 4: return new Gun();
case 5: return new Dagger();
case 6: return new Hammer();
...

새로운 제품 추가 시 Factory 코드를 “반드시” 수정해야 함
→ OCP(개방-폐쇄 원칙) 위반
그래서 실무에서는 단순 팩토리 대신

- 팩토리 메서드 패턴
- 추상 팩토리 패턴

을 사용하여 이 문제를 해결한다.
즉, 단순 팩토리는 실제 개발에서 바로 사용되기보다 팩토리 계열 패턴을 이해하기 위한 기초 단계라고 할 수 있다.
단순 팩토리 패턴은 디자인 패턴 중 가장 기본적인 생성 패턴으로서객체 생성의 책임을 전담 클래스에 맡겨, 생성과 사용을 분리하는 구조적 장점을 제공한다.
비록 확장성 면에서는 한계가 있지만,설계 초반이나 작은 프로젝트에서는 매우 유용하다
