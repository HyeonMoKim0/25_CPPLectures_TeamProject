달러를 한국 원화로 변환하는 어댑터 패턴, 추후 어댑터의 geyMoney() 함수를 포함하여 다른 부분들도 손 볼 예정
```C++
// 달러를 한국 원화로 변환하는 어댑터 패턴
#include <iostream>
#include <algorithm>
using namespace std;

enum ISO { // 각종 국가를 대상으로 환전을 원할 경우, 해당 enum에 국가 코드와 번호를 입력하면 됨(확장성 증가)
	USD = 840
};

class KoreanMoney {
private:
	double won;

public:
	KoreanMoney() = default;
	KoreanMoney(double w) : won(w) {}
	virtual ~KoreanMoney() = default;

	double getWon() const { return won; }
	void setWon(double w) { won = w; }

	virtual double getMoney() = 0;
};

// Adaptee(인터페이스와 맞지 않는 기존 클래스)의 역할
class USDMoney {
private:
	double dollar;
	const ISO ISOCode = ISO::USD; // USD의 ISO4217 코드
public:
	USDMoney(double d) : dollar(d) {}

	double getDollar() const { return dollar; }
	ISO getISOCode() const { return ISOCode; }

	double operator()() const { return getDollar(); } // 굳이 getDollar() 대신 연산자 오버로딩한 이유는, 다른 통화 클래스들과의 일관성을 위함
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
		//exchange(); //환전하는 시점은 getMoney() 호출할 때마다 하는 것이 어댑터 패턴의 일반적인 흐름이라 함
		explain();
	}
	virtual ~ExchangeMoney() = default;

	virtual double getMoney() override { 
		exchange();
		return getWon(); 
	}

	void exchange() {
		exchangeMoney = foreignCurrency() * exchangeRate;
		setWon(exchangeMoney);
	}

	void setExchangeRate() {
		switch (isoCode) {
		case (ISO::USD):
			exchangeRate = (double)1465.00; // 1 USD = 1465 KRW
			break;
		default:
			cout << "지원하지 않는 통화로, 현재 환전이 불가능합니다.\n 프로그램을 종료합니다." << endl;
			exit(1);
			break;
		}
	}

	void explain() {
		cout << "환전 통화 ISO4217 코드: " << isoCode << endl;
		cout << "환전 통화 금액: " << foreignCurrency() << endl;
		cout << "환율: " << exchangeRate << endl;
		cout << "환전된 원화 금액: " << getMoney() << "원" << endl;
	}
};

int main() {
	USDMoney myDollar(100); // 100달러 생성
	KoreanMoney* myWon = new ExchangeMoney<USDMoney>(myDollar); // 어댑터로 환전된 원화 객체 생성

	cout << myWon->getMoney() << "원 환전 완료!" << endl;
	
	delete myWon;
	return 0;
}
```
