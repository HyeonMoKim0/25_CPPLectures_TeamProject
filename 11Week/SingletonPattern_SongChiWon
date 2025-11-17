해당 코드 작성자: 송치원
```C++
#include <iostream>
using namespace std;

class Sample {
private:
	int data = 0;
	Sample() {}
	Sample(const Sample&) = delete; // 복사 생성자 삭제
	Sample& operator+(const Sample&) = delete; // 할당 연산자 삭제
public:
	//객체 참조를 통해 유일한 객체의 주소 반환
	static Sample& getInstance() { 
		static Sample instance; // 최초로 호출될 때 까지 대기
		// 필요해질 때까지 자원 할당을 미룸
		return instance;
	}

	void increaseData() { data++; }
	int getData() { return data; }
};

int main() {
	Sample& s1 = Sample::getInstance();
	cout << s1.getData() << endl;

	s1.increaseData();
	s1.increaseData();

	Sample& s2 = Sample::getInstance();

	s2.increaseData();
	cout << s2.getData() << endl;
}
```
