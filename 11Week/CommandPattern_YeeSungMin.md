해당 코드 작성자: 이성민
```C++
#include <iostream>
using namespace std;

// Receiver : 실제 일을 하는 클래스
class Light {
public:
    void on() {
        cout << "Light ON\n";
    }
    void off() {
        cout << "Light OFF\n";
    }
};

// Command 인터페이스
class Command {
public:
    virtual ~Command() {}
    virtual void execute() = 0;  // 공통으로 실행할 함수
};

// Concrete Command : 불을 켜는 명령
class LightOnCommand : public Command {
    Light& light;    // 어떤 Light에 대해 동작할지 참조 보관
public:
    LightOnCommand(Light& l) : light(l) {}
    void execute() override {
        light.on();  // 실제 작업은 Receiver에게 위임
    }
};

// Concrete Command : 불을 끄는 명령
class LightOffCommand : public Command {
    Light& light;
public:
    LightOffCommand(Light& l) : light(l) {}
    void execute() override {
        light.off();
    }
};

// Invoker : Command를 가지고 있다가 필요할 때 실행만 시킴
class Button {
    Command* command;   // 소유(X), 그냥 실행만 하는 포인터
public:
    Button() : command(nullptr) {}

    void setCommand(Command* cmd) {
        command = cmd;
    }

    void press() {
        if (command) {
            command->execute();  // 무슨 동작인지는 모르고 그냥 실행만
        }
    }
};

int main() {
    Light livingRoomLight;

    // 구체적인 명령 객체 생성 (스택에 생성)
    LightOnCommand  lightOn(livingRoomLight);
    LightOffCommand lightOff(livingRoomLight);

    Button button;

    // 버튼에 "불 켜기" 명령 연결
    button.setCommand(&lightOn);
    button.press();   // → Light ON

    // 버튼에 "불 끄기" 명령 연결
    button.setCommand(&lightOff);
    button.press();   // → Light OFF

    return 0;
}
```
- Light는 on(), off() 같은 실제 기능을 수행하는 클래스임
- LightOnCommand와 LightOffCommand
  - Light 객체를 참조로 저장함
  - execute()에서 light.on() 또는 light.off()를 호출함
- Button은 Command*만 알고 있는 상태
  - setCommand()로 어떤 명령을 사용할지 주입받고 있음
  - press()에서 단순히 execute()만 호출하고 있음
- main()
  - 명령 객체를 생성하고 있음
  - 버튼에 해당 명령을 연결하고 있음
  - 같은 버튼으로 “켜기 → 끄기” 동작을 자유롭게 교체하고 있음
