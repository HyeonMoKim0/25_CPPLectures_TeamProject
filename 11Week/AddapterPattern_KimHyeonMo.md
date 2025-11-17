해당 코드 작성자: 김현모

다음은 어댑터 패턴의 핵심인 변환을, 섭씨 온도에서 화씨 온도로 변화하는 예제임.
```C++
#include <iostream>

class Celsius { // 섭씨 온도 클래스
public:
    double getCelsius() const { return 30.0; } // 현재 온도 반환
};

class Fahrenheit { // 화씨 온도 클래스
public:
    virtual double getFahrenheit() = 0;
};

class Adapter : public Fahrenheit { // 섭씨를 화씨로 변환
	Celsius adapter; //섭씨를 화씨로 변환하기 위해, 섭씨 온도 클래스의 객체 포인터 선언
	double c; // 섭씨 온도 저장 변수

public:
    Adapter(const Celsius& cel) : adapter(cel) {}

    double getFahrenheit() override { // 섭씨를 화씨 온도로 변환 및 반환
        c = adapter.getCelsius(); // 어댑터의 역할인, 변환 작업 발생
        return (c * 1.8) + 32;
    }
};

int main() {
    Celsius cel;

    Adapter adapter(cel);

    std::cout << "현재 섭씨 온도: " << cel.getCelsius() << std::endl <<
        "변환한 화씨 온도: " << adapter.getFahrenheit() << std::endl;

    return 0;
}
```
