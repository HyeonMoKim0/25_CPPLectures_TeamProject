```C++
#include <iostream>
#include <stack>

using namespace std;

class Player {
    int health;
public:
    Player() = delete;
	Player(int hp) : health(hp) {}
    void Jump() { cout << "[Player] 점프!" << endl; }
    void Attack() { cout << "[Player] 공격!" << endl; }
};

class Command {
public:
    virtual ~Command() = default;
    virtual void execute() = 0;
    virtual void undo() = 0;
	virtual void redo() = 0;
};

class JumpCommand : public Command {
private:
    Player* player;
public:
    JumpCommand(Player* p) : player(p) {}
    void execute() override { if (player) player->Jump(); }
	void undo() override { if (player) cout << "[Player] 점프 취소!" << endl; }
    void redo() override { if (player) { cout << "[redo] "; player->Jump(); } }
};

class AttackCommand : public Command {
private:
    Player* player;
public:
    AttackCommand(Player* p) : player(p) {}
    void execute() override { if (player) player->Attack(); }
	void undo() override { if (player) cout << "[Player] 공격 취소!" << endl; }
    void redo() override { if (player) { cout << "[redo] "; player->Attack(); } }
};

class InputHandler {
private:
	stack<Command*> commandHistory;
	stack<Command*> redoStack;
    Command* jumpCmd;
    Command* attackCmd;
public:
    InputHandler(Command* jump, Command* attack)
        : jumpCmd(jump), attackCmd(attack) {
    }

    void handleInput(char key) {
        switch (key) {
        case 'j':
        case 'J':
            if (jumpCmd)
            {
                jumpCmd->execute();
                commandHistory.push(jumpCmd);
				while (!redoStack.empty()) redoStack.pop();
            }
            break;
        case 'a':
        case 'A':
            if (attackCmd)
            {
                attackCmd->execute();
                commandHistory.push(attackCmd);
                while (!redoStack.empty()) redoStack.pop();
            }
            break;
        case 'u':
		case 'U':
            if (!commandHistory.empty())
            {
                Command* lastCommand = commandHistory.top();
				redoStack.push(lastCommand);
                commandHistory.pop();
                lastCommand->undo();
            }
            else
            {
				cout << "실행된 명령이 없습니다." << endl;
            }
            break;
		case 'r':
		case 'R':
            if (!redoStack.empty())
            {
                Command* lastCommand = redoStack.top();
                commandHistory.push(lastCommand);
                redoStack.pop();
                lastCommand->redo();
            }
            else
            {
                cout << "실행된 명령이 없습니다." << endl;
            }
            break;
        default:
            cout << "지원하지 않는 입력입니다." << endl;
            break;
        }
    }
};

int main() {
    Player player(100);

    JumpCommand jump(&player);
    AttackCommand attack(&player);

    InputHandler input(&jump, &attack);

    cout << "=== Command Pattern Example ===" << endl;
    cout << "j / J : Jump" << endl;
    cout << "a / A : Attack" << endl;
	cout << "u / U : Undo last command" << endl;
	cout << "r / R : Redo last command" << endl;
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
