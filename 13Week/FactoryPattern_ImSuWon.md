앞으로의 팩토리 패턴으로 개발할 코드  
1. 설계 개요  
이번 주차에서는 파일 처리기를 구현하기 위한 준비 단계로 CSV 파일을 읽는 파서(CsvParser) 를 먼저 개발하였다.  
확장자에 따라 서로 다른 파서를 선택할 수 있도록 팩토리 메서드 패턴 + 등록(Registry) 기반 팩토리 구조를 먼저 구축하고,  
그 구조를 검증하기 위해 가장 기본적인 CSV 파싱 기능부터 실제로 구현하였다.  
JSON과 XML은 이번 주차에는 구현하지 않았으며, 다음 주에 팩토리 구조를 그대로 유지한 채 간단히 추가할 계획이다.  
  
2. 파일 파서 구조  
(1) FileParser 추상 클래스  
모든 파서가 공통으로 가져야 하는 Parse() 함수를 가진다.  
각 파일 형식별 파서는 이 클래스를 상속받아 독립적으로 구현된다.  
(2) CSV 파서 구현 (이번 주 구현 범위)  
ifstream으로 파일을 열고 한 줄씩 읽어서, 기준으로 파싱하여 토큰 출력  
학습용으로 제작된 단순 CSV 파서지만, 실제로 파일을 열고 처리하는 기능이 포함되어 있다.  
JSON/XML은 이번 주차 구현 범위에 포함되지 않음 (다음 주에 추가 예정)  
  
3. 팩토리 패턴 구조  
이번 주차의 핵심은 "확장자 → 파서 클래스 매핑" 을 가능한 구조를 만드는 것이다.  
(1) 팩토리 메서드 역할  
각 파서를 생성하는 함수(CreateCsvParser 등)를 팩토리 메서드로 사용했다.  
(2) 등록 기반 ParserFactory  
map<string, 생성함수> 형태로 확장자를 등록할 수 있는 구조를 만들었다.  
예:  
"csv" → CSV 파서 생성 함수 등록  
이 구조를 사용하면 다음 주에 JSON/XML을 추가할 때 factory 코드를 수정할 필요가 없다.  
factory.Register("json", &CreateJsonParser);  
factory.Register("xml",  &CreateXmlParser);  
이 한 줄만으로 기능 확장이 가능하도록 설계했다.  
4. 이번 주 전체 동작 흐름  
사용자에게 CSV 파일 이름을 입력받고 확장자를 분석해 “csv”인 경우 ParserFactory가 CsvParser를 생성  
CsvParser가 파일을 열고 내용을 실제로 파싱함  
현재는 CSV만 등록해두었기 때문에 확장자가 csv가 아닌 경우 파서를 생성하지 않음.  
  
5. 결론  
이번 주에는 팩토리 패턴 기반 구조 + CSV 파서 구현까지 완료하였다.  
추상 클래스 기반 파서 구조 정립  
확장자 기반 파서 선택 팩토리 구현  
CSV 파서를 실제로 동작하도록 작성  
다음 주 추가될 포맷(JSON/XML)의 기반이 되는 구조 완성  
JSON과 XML은 의도적으로 이번 주 구현 범위에서 제외하고, 다음 주차에 기능을 자연스럽게 확장하도록 설계하였다.  

```C++
// FileParserFactory_CSV.cpp
// 이번 주차 구현 범위: CSV 파서 + 팩토리 패턴 기반 구조
// JSON / XML 파서는 다음 주차에 추가 예정

#include <iostream>
#include <string>
#include <memory>
#include <map>
#include <fstream>
#include <sstream>
#include <vector>

using namespace std;

//---------------------------------------------------------
// 1. 파일 파서 추상 클래스 (인터페이스)
//---------------------------------------------------------
class FileParser {
public:
    virtual ~FileParser() {}
    virtual void Parse(const string& path) = 0;
};

//---------------------------------------------------------
// 2. CSV 파서 구현 (이번 주 구현 범위)
//---------------------------------------------------------
class CsvParser : public FileParser {
public:
    void Parse(const string& path) override {
        ifstream file(path);
        if (!file.is_open()) {
            cout << "[CSV] 파일을 열 수 없습니다: " << path << endl;
            return;
        }

        cout << "===== CSV 파싱 시작 (" << path << ") =====" << endl;

        string line;
        int lineNum = 0;

        while (getline(file, line)) {
            ++lineNum;

            stringstream ss(line);
            string token;
            vector<string> tokens;

            while (getline(ss, token, ',')) {
                tokens.push_back(token);
            }

            cout << lineNum << "행: ";
            for (auto& t : tokens)
                cout << "[" << t << "] ";
            cout << endl;
        }

        cout << "===== CSV 파싱 종료 =====" << endl;
    }
};

//---------------------------------------------------------
// 3. CSV 생성 함수 (팩토리 메서드 역할)
//---------------------------------------------------------
using ParserCreator = unique_ptr<FileParser>(*)();

unique_ptr<FileParser> CreateCsvParser() {
    return make_unique<CsvParser>();
}

//---------------------------------------------------------
// 4. 등록 기반 ParserFactory
//---------------------------------------------------------
class ParserFactory {
    map<string, ParserCreator> creators;   // "csv" → CreateCsvParser

public:
    void Register(const string& ext, ParserCreator creator) {
        creators[ext] = creator;
    }

    unique_ptr<FileParser> Create(const string& ext) const {
        auto it = creators.find(ext);
        if (it == creators.end()) {
            cout << "지원하지 않는 확장자입니다: " << ext << endl;
            return nullptr;
        }
        return (it->second)();
    }

    void PrintRegistered() const {
        cout << "등록된 파서: ";
        for (auto& p : creators)
            cout << p.first << " ";
        cout << endl;
    }
};

//---------------------------------------------------------
// 5. CSV 파서만 등록하는 함수
//---------------------------------------------------------
void RegisterCsvOnly(ParserFactory& factory) {
    factory.Register("csv", &CreateCsvParser);
}

//---------------------------------------------------------
// 6. 메인 함수
//---------------------------------------------------------
int main() {
    ParserFactory factory;

    RegisterCsvOnly(factory);  // 이번 주는 CSV만 등록
    factory.PrintRegistered();

    while (true) {
        cout << "\n파싱할 파일 이름을 입력하세요 (예: data.csv)   (quit 입력 시 종료)\n>>> ";

        string filename;
        cin >> filename;

        if (filename == "quit")
            break;

        // 확장자 추출
        size_t dotPos = filename.rfind('.');
        if (dotPos == string::npos) {
            cout << "확장자를 찾을 수 없습니다." << endl;
            continue;
        }

        string ext = filename.substr(dotPos + 1);

        // CSV 파서 생성
        auto parser = factory.Create(ext);
        if (!parser)
            continue;

        // 실제 파일 처리
        parser->Parse(filename);
    }

    return 0;
}
```
