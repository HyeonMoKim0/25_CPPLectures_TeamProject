싱글톤 패턴을 사용할 때 주위해야 할 점 확인
- 싱글톤 패턴을 구현할 때 private 생성자와 static 메서드는 클래스를 고립시키기 때문에 테스트를 할 때 단위적으로 테스트가 불가능함 
- 객체의 복사를 막아 단일 인스턴스를 보장하기 때문에 복사 생성자 및 대입 연산자를 사용하지 않음
- 싱글톤 인스턴스는 프로그램 전체에서 공유되기에 전역 변수처럼 잘못 사용될 위험이 있음
- 싱글톤을 상속받아 기능을 확장하고 싶을 때도 private 생성자 때문에 호출하는 것이 복잡해져 확장성이 떨어짐


```C++
#include <iostream>
#include <string>
using namespace std;

class Background {
private:
    string theme;
                                                                                            
    Background() : theme("Default") {} // 1.
    Background(const Background&) = delete; // 2.
    Background& operator=(const Background&) = delete;

public:
  
    static Background& getInstance() { // 3.
        static Background instance;
        return instance;
    }

    void setTheme(const string& newTheme) {
        theme = newTheme;
        cout << "current background: " << theme << endl;
    }

    string getTheme() const {
        return theme;
    }
};

int main() {

    Background& config1 = Background::getInstance();
    cout << "config1: " << config1.getTheme() << endl; 

    config1.setTheme("Dark Mode");

    Background& config2 = Background::getInstance();
    cout << "config2: " << config2.getTheme() << endl; 


    return 0;
}
// 두번째 객체에서 getInstance()로 반환된 값은 
// 첫번째 객체에서 반환된 값과 동일함 
```
