```C++
// FileParserFactory_Multi.cpp
// CSV / JSON / XML 파일을 파싱하는 파일 포맷 처리기 (팩토리 메서드 패턴)

#include <iostream>
#include <string>
#include <memory>
#include <map>
#include <fstream>
#include <sstream>
#include <vector>

using namespace std;

// 1. 공통 인터페이스: 파일 파서 추상 클래스
class FileParser {
public:
    virtual ~FileParser() {}
    virtual void Parse(const string& path) = 0;
};

// 2-1. CSV 파서 구현
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

// 2-2. JSON 파서 구현 
class JsonParser : public FileParser {
public:
    void Parse(const string& path) override {
        ifstream file(path);
        if (!file.is_open()) {
            cout << "[JSON] 파일을 열 수 없습니다: " << path << endl;
            return;
        }

        cout << "===== JSON 파싱 시작 (" << path << ") =====" << endl;

        // 파일 전체를 하나의 문자열로 읽기
        string content((istreambuf_iterator<char>(file)),
            istreambuf_iterator<char>());

        int braceOpen = 0;   // '{'
        int braceClose = 0;  // '}'
        int colonCount = 0;  // ':'

        for (char c : content) {
            if (c == '{')      braceOpen++;
            else if (c == '}') braceClose++;
            else if (c == ':') colonCount++;
        }

        cout << "파일 내용:" << endl;
        cout << content << endl << endl;

        cout << "여는 중괄호 '{' 개수: " << braceOpen << endl;
        cout << "닫는 중괄호 '}' 개수: " << braceClose << endl;
        cout << "키-값 구분 ':' 개수: " << colonCount << endl;

        cout << "===== JSON 파싱 종료 =====" << endl;
    }
};

// 2-3. XML 파서 구현 
class XmlParser : public FileParser {
public:
    void Parse(const string& path) override {
        ifstream file(path);
        if (!file.is_open()) {
            cout << "[XML] 파일을 열 수 없습니다: " << path << endl;
            return;
        }

        cout << "===== XML 파싱 시작 (" << path << ") =====" << endl;

        string line;
        int lineNum = 0;

        while (getline(file, line)) {
            ++lineNum;

            size_t start = line.find('<');
            size_t end = line.find('>');

            if (start != string::npos && end != string::npos && end > start + 1) {
                string tag = line.substr(start + 1, end - start - 1);
                cout << lineNum << "행 태그: <" << tag << ">" << endl;
            }
        }

        cout << "===== XML 파싱 종료 =====" << endl;
    }
};

// 3. 팩토리 메서드용 타입 & 생성 함수
using ParserCreator = unique_ptr<FileParser>(*)();

unique_ptr<FileParser> CreateCsvParser() { return make_unique<CsvParser>(); }
unique_ptr<FileParser> CreateJsonParser() { return make_unique<JsonParser>(); }
unique_ptr<FileParser> CreateXmlParser() { return make_unique<XmlParser>(); }

// 4. 등록 기반 팩토리
class ParserFactory {
    map<string, ParserCreator> creators;   // "csv" -> CreateCsvParser 등

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
        for (const auto& p : creators)
            cout << p.first << " ";
        cout << endl;
    }
};

// 5. CSV / JSON / XML 모두 등록
void RegisterAllParsers(ParserFactory& factory) {
    factory.Register("csv", &CreateCsvParser);
    factory.Register("json", &CreateJsonParser);
    factory.Register("xml", &CreateXmlParser);
}

// 6. 메인 함수
int main() {
    ParserFactory factory;

    RegisterAllParsers(factory);
    factory.PrintRegistered();

    while (true) {
        cout << "\n파싱할 파일 이름을 입력하세요 (예: data.csv / conf.json / layout.xml)\n"
            << "(quit 입력 시 종료)\n>>> ";

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

        string ext = filename.substr(dotPos + 1); // csv / json / xml

        auto parser = factory.Create(ext);
        if (!parser)
            continue;

        parser->Parse(filename);
    }

    return 0;
}
```
