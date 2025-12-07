```C++
#include <iostream>
#include <string>

class IDGenerator {
private:
    // ID 저장
    long long currentID;

    // 외부에서 인스턴스 생성 금지
    IDGenerator() : currentID(1000) {
        std::cout << "시작 ID: 1001" << std::endl;
    }

    // 복사 및 대입 연산자를 삭제하여 복제 방지
    IDGenerator(const IDGenerator&) = delete;
    IDGenerator& operator=(const IDGenerator&) = delete;

public:
    // 유일한 인스턴스에 접근하는 static 메서드
    static IDGenerator& getInstance() {
        static IDGenerator instance;
        return instance;
    }

    // 다음 ID 발급 
    long long generateNextID() {
        return ++currentID;
    }

    // 현재 ID 상태를 확인 (디버깅)
    long long getCurrentID() const {
        return currentID;
    }
};


// ID를 사용하는 모듈 A
void useIDInModuleA() {
    // 모든 모듈은 getInstance()를 통해 동일한 ID 생성기를 사용
    long long id1 = IDGenerator::getInstance().generateNextID();
    long long id2 = IDGenerator::getInstance().generateNextID();

    std::cout << "A Generator ID 1 : " << id1 << std::endl;
    std::cout << "A Generator ID 2: " << id2 << std::endl;
}

// ID를 사용하는 모듈 B
void useIDInModuleB() {
    long long id = IDGenerator::getInstance().generateNextID();
    std::cout << "B Generator ID: " << id << std::endl;
}


int main() {

    // 모듈 A에서 ID 사용
    useIDInModuleA();

    // 모듈 B에서 ID 사용
    useIDInModuleB();

    // 메인 함수에서 직접 ID 사용
    long long finalID = IDGenerator::getInstance().generateNextID();
    std::cout << "Final ID: " << finalID << std::endl;

    // 모든 호출이 동일한 카운터를 공유했는지 확인
    std::cout << "\n--- 최종 상태 확인 ---" << std::endl;
    std::cout << "ID Generator가 가진 최종 카운터 값: " << IDGenerator::getInstance().getCurrentID() << std::endl;


    return 0;
}
```
