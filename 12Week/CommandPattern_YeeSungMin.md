```C++
#include <iostream>
#include <memory>
using namespace std;

class Player {
public:
    void Jump() { cout << "[Player] 점프!" << endl; }
    void Attack() { cout << "[Player] 공격!" << endl; }
};

class Command {
public:
    virtual ~Command() = default;
    virtual void execute() = 0;
};

class JumpCommand : public Command {
private:
    Player* player;
public:
    JumpCommand(Player* p) : player(p) {}
    void execute() override { if (player) player->Jump(); }
};

class AttackCommand : public Command {
private:
    Player* player;
public:
    AttackCommand(Player* p) : player(p) {}
    void execute() override { if (player) player->Attack(); }
};

class InputHandler {
private:
    Command* jumpCmd;
    Command* attackCmd;
public:
    InputHandler(Command* jump, Command* attack)
        : jumpCmd(jump), attackCmd(attack) {}

    void handleInput(char key) {
        switch (key) {
        case 'j':
        case 'J':
            if (jumpCmd) jumpCmd->execute();
            break;
        case 'a':
        case 'A':
            if (attackCmd) attackCmd->execute();
            break;
        default:
            cout << "지원하지 않는 입력입니다." << endl;
            break;
        }
    }
};

int main() {
    Player player;

    JumpCommand jump(&player);
    AttackCommand attack(&player);

    InputHandler input(&jump, &attack);

    cout << "=== Command Pattern Example ===" << endl;
    cout << "j / J : Jump" << endl;
    cout << "a / A : Attack" << endl;
    cout << "q / Q : Quit" << endl;

    while (true) {
        cout << "입력: ";
        char ch;
        if (!(cin >> ch)) break;

        if (ch == 'q' || ch == 'Q') {
            cout << "프로그램을 종료합니다." << endl;
            break;
        }

        input.handleInput(ch);
    }

    return 0;
}
```
