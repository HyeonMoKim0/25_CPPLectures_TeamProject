1 내가 이해한 커맨드 패턴 구조  
커맨드 패턴은 기능을 객체로 캡슐화하여 입력 처리 코드와 실제 동작을 분리하는 구조이다  
점프 공격 같은 기능을 각각 독립적인 커맨드 클래스로 만들고 입력 처리는 어떤 키가 눌렸는지만 확인한다  
이렇게 하면 기능을 추가할 때 입력 처리 코드를 수정할 필요가 거의 없고 확장성이 좋아진다  
  
  
2 Undo와 Redo의 기본 원리  
커맨드를 기록해 두었다가 되돌리고 다시 실행하는 방식이다  
Undo는 실행된 명령 중 가장 최근 명령을 가져와서 되돌리고 Redo 스택에 저장한다  
Redo는 Redo 스택에서 다시 명령을 꺼내 실행하며 히스토리에 되돌려 놓는다  
두 스택 사이에서 커맨드가 이동하며 되돌리기와 다시 실행 기능이 완성된다  
  
  
3 새로운 명령이 실행될 때 Redo 기록을 비우는 이유  
Undo 후 새로운 명령을 실행하면 예전 Redo 정보는 의미가 없어진다  
그래서 일반적인 프로그램처럼 Redo 스택을 비워서 상태의 일관성을 유지해야 한다  
  
  
4 매 실행마다 새로운 커맨드 객체를 만들어야 하는 이유  
명령마다 서로 다른 상태가 필요할 수 있다  
예를 들어 이동 명령은 이동 전 위치와 이동 후 위치가 필요하고 이런 정보는 명령마다 다르게 저장된다  
따라서 점프 명령이나 공격 명령도 매번 새 객체를 만들어 히스토리에 저장해야 한다  
이렇게 해야 모든 명령이 독립적인 상태를 가질 수 있다  
  
  
5 Undo와 Redo 코드 흐름 이해  
입력 처리기는 키 입력을 받을 때마다 새로운 커맨드 객체를 만들고 히스토리에 저장한다  
Undo를 하면 히스토리에서 명령을 꺼내 Redo 스택으로 옮기고 취소 동작을 수행한다  
Redo를 하면 Redo 스택에서 명령을 꺼내 다시 실행하고 히스토리에 넣는다  
두 스택이 서로 반대로 동작하면서 되돌리기와 다시 실행 흐름을 만든다  
  
  
6 메모리 관리에서 알게 된 점  
새 객체를 계속 생성하기 때문에 히스토리와 Redo 스택에서 제거할 때 삭제가 필요하다  
학습용 예제에서는 프로그램 종료 시 운영체제가 메모리를 정리해 주지만  
실제 프로그램에서는 스택을 모두 비우면서 객체도 삭제해야 메모리 누수가 발생하지 않는다  
  
  
7 조건문에서 중괄호를 생략하면 생기는 문제  
중괄호를 생략하면 조건문 바로 뒤의 한 줄만 조건에 포함된다  
출력문만 조건 안에 들어가고 실제 동작인 점프나 공격은 조건과 상관없이 실행될 수 있다  
이런 문제를 방지하려면 중괄호를 반드시 사용하는 것이 안전하다는 점을 다시 확인했다  
  
  
8 스택의 주요 함수 정리  
push  
가장 위에 새로운 요소를 추가한다  
  
top  
가장 위의 요소를 반환하지만 제거하지는 않는다  
  
pop  
가장 위의 요소를 제거한다  
Undo를 구현할 때 top을 먼저 조회하고 pop을 수행하는 것이 중요하다  
  
empty  
스택이 비어 있는지 확인한다  
  
size  
스택 안에 들어 있는 요소의 개수를 확인한다  
  
이 함수들이 조합되어 히스토리 관리와 Undo Redo 흐름을 만든다  
  
  
9 커맨드 객체의 크기 이해  
가상 함수가 포함된 클래스는 내부적으로 가상 함수 테이블을 가리키는 포인터를 가진다  
내가 구현한 객체는 대략 24바이트를 가지고 있었고,  
스택에는 커맨드 자체가 아니라 포인터가 저장되므로 객체 하나를 생성하고 히스토리에 저장하면 약 32바이트가 필요했다.  
  
  
다음 주차 개발 예정  
1. 현재 명령들은 출력만 하기 때문에 Undo와 Redo가 논리적으로만 동작한다  
이를 개선하기 위해 체력 위치 이동 등 실제 상태가 변하는 명령을 추가해 볼 예정이다  
이렇게 하면 커맨드 패턴이 상태를 저장하고 되돌리는 과정을 더 깊게 익힐 수 있을 것이다  
2. 지금은 점프 명령과 공격 명령을 하나의 객체로 재사용하고 있지만  
각 명령이 독립적인 상태를 가질 수 있도록 매번 새로운 객체를 만드는 구조로 확장할 예정이다  
이 방식은 이동 명령처럼 이전 상태와 이후 상태를 모두 저장해야 할 때 더 유용할 것이다  
3. 플레이어의 위치를 변경하는 이동 명령을 만들고  
Undo와 Redo로 실제 좌표가 복구되는 기능을 구현해 볼 예정이다  
이 과정을 통해 커맨드 패턴의 전체 흐름을 실제 동작 기반으로 이해할 수 있을 것이다  
4. 더 나은 키 바인딩이나 사용을 위해서 Ai를 이용해 물어본 결과 우리가 배운 map을 사용해서  
구현이 가능하다고 한다. 이를 이용하여 키 바인딩이나 입력 확장에 고민해 볼 계획이다.  
5. 동적 객체를 계속 생성하는 구조로 바꾸면 명령 객체를 삭제하는 과정도 필요하다  
이를 위해 히스토리와 Redo 스택을 비우면서 객체를 삭제하거나  
스마트 포인터를 사용하는 방식으로 정리 단계를 구현해 볼 예정이다  

---
아래는 이번에 구현한 코드다.
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
