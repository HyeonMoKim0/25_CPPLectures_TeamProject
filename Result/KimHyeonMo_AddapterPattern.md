```C++
// 달러를 한국 원화로 변환하는 어댑터 패턴 예제
#include <iostream>
using namespace std;

enum ISO {
	USD = 840
	// 각종 여러 나라 국가 코드 등등..
};

class KoreanMoney {
public:
	virtual ~KoreanMoney() = default;
	virtual double getMoney() = 0;
};

// Adaptee(인터페이스와 맞지 않는 기존 클래스)
class USDMoney {
private:
	double dollar;
	const ISO ISOCode = ISO::USD; // USD의 ISO4217 코드
public:
	USDMoney(double d) : dollar(d) {}

	double getMoney() const { return dollar; }
	ISO getISOCode() const { return ISOCode; }
};

template <typename T>
class ExchangeMoney : public KoreanMoney {
private:
	double exchangeMoney;	// 환전 금액 저장용
	double exchangeRate;	// 환율 저장용
	T foreignCurrency;		// 외화 객체
	ISO isoCode;

public:
	ExchangeMoney(T foreignCurrency)
		: foreignCurrency(foreignCurrency), isoCode(foreignCurrency.getISOCode()) {
		setExchangeRate();
		explain();
	}
	virtual ~ExchangeMoney() = default;

	virtual double getMoney() override {
		exchange(); // 해당 함수를 호출할 때마다 환전하는 것이 어댑터 패턴의 일반적인 흐름
		return exchangeMoney;
	}

	void exchange() {
		setExchangeRate();
		exchangeMoney = foreignCurrency.getMoney() * exchangeRate;
	}

	void setExchangeRate() {
		switch (isoCode) {
		case (ISO::USD):
			exchangeRate = (double)1465.00; // 1 USD = 1465 KRW
			break;
		default:
			cout << "현재 미지원하는 통화로, 환전 불가능.\n 프로그램 종료" << endl;
			exit(1);
			break;
		}
	}

	void explain() {
		cout << "환전 통화 ISO4217 코드: " << isoCode << endl;
		cout << "환전 통화 금액: " << foreignCurrency.getMoney() << endl;
		cout << "환율: " << exchangeRate << endl;
		cout << "환전된 원화 금액: " << getMoney() << "원" << endl;
	}
};

int main() {
	USDMoney myDollar(100); // 100달러 생성
	KoreanMoney* myWon1 = new ExchangeMoney<USDMoney>(myDollar); // 어댑터로 환전된 원화 객체 생성
	cout << myWon1->getMoney() << "원 환전 완료" << endl;
	cout << endl;

	KoreanMoney* myWon2 = new ExchangeMoney<USDMoney>(USDMoney(150)); // 외화 객체 선언 없이 생성
	cout << myWon2->getMoney() << "원 환전 완료" << endl;

	delete myWon1;
	delete myWon2;
	return 0;
}
```
