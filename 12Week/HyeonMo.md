해당 코드에서는 3가지 헤더파일을 포함해야 하며, 이를 .cpp 코드에 활용할 수 있음

- Target Interface(현재 사용하는 인터페이스), NewController.h 이라는 헤더파일로 활용
```C++
// Target Interface(현재 사용하는 인터페이스)
class NewConsole {
public:
    virtual ~NewConsole() = default;
    virtual void playGame() = 0;
};
```

- Adaptee(인터페이스와 맞지 않는 기존 클래스), OldController.h 이라는 헤더파일로 활용
```C++
#include <iostream>

// Adaptee(인터페이스와 맞지 않는 기존 클래스)
class OldController {
public:
    void activateOldDevice() { std::cout << "Old Controller: 구형 장치 작동 시작..." << std::endl; }
    void pressButton() { std::cout << "Old Controller: 버튼 누르기 (Old Style)" << std::endl; }
};
```

- Adapter(어댑티 객체를 현재 사용하는 인터페이스로 변환하는 클래스), ControllerAdapter.h 이라는 헤더파일로 활용
```C++
#include "NewController.h"
#include "OldController.h"
#include <iostream> //이미 OldController.h에서 포함되었지만 안전하게 다시 포함, 또 포함해도 중복 정의 방지되는 헤더 가드 처리가 되어 있음

// Adapter(어댑티 객체를 현재 사용하는 인터페이스로 변환하는 클래스)
class ControllerAdapter : public NewConsole {
private:
    OldController* oldDevice;

public:
    // 어댑터 생성 시, 변환할 구형 장치(Adaptee)를 받습니다.
    ControllerAdapter(OldController* device) : oldDevice(device) {}

    // Target Interface의 메서드를 구현합니다.
    void playGame() override {
        // 클라이언트가 'playGame'을 호출하면,
        // 내부적으로 OldController의 메서드를 순서대로 호출하여 변환합니다.

        std::cout << " Adapter: NewConsole 요청을 OldController에 맞게 변환 중..." << std::endl;
        oldDevice->activateOldDevice();
        oldDevice->pressButton();
        std::cout << " Adapter: 게임 플레이 요청 처리 완료." << std::endl;
    }
};
```

- .cpp에서 활용하는 코드
```C++
#include <iostream> //이미 다른 헤더 파일에서 포함되었지만 안전하게 다시 포함, 또 포함해도 중복 정의 방지되는 헤더 가드 처리가 되어 있음
#include <memory>
#include "ControllerAdapter.h"
using namespace std;

void clientOperation(NewConsole* console) {
    cout << "--- 클라이언트: 새로운 게임 시작 요청 ---" << endl;
    console->playGame();
    cout << "------------------------------------" << endl;
}

int main() {
    OldController myOldController; // Adaptee 객체 선언

    unique_ptr<NewConsole> adapter = 
        make_unique<ControllerAdapter>(&myOldController); //어댑터 객체 생성 및 어댑티 객체 삽입

    clientOperation(adapter.get()); // NewConsole 인터페이스만 알고 있고, 실제로는 OldController를 사용하는 어댑터와 상호작용

    return 0;
}
```
