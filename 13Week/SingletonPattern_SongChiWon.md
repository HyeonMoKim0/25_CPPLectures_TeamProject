싱글톤이 적합하게 사용되는 시나리오 중 ID 생성기 프로그램을 재채택  
ID 생성기 프로그램 작성중 주의사항: 경쟁상태 방지  
경쟁상태: 멀티스레드 환경에서 여러 스레드가 동시에 공유자원에 접근하여 데이터가 섞이거나 손실되는 문제  
방지법: 한번에 하나의 스레드만 공유자원에 접근하도록 동기화 매커니즘을 사용 - Mutex  
Mutex: 자원에 대한 '락'을 설정하는 기능으로 뮤텍스를 성공적으로 획득(Lock)한 스레드만 자원에 접근할 수 있는 소유권을 가지게 하고 이미 다른 스레드가 뮤텍스를 획득했을 경우 다른 스레드는 락이 풀릴 때까지(Unlock) 대기  

```C++
#include <iostream>
#include <thread>
#include <mutex>
#include <vector>
using namespace std;

int shared_counter = 0; // 공유 자원
mutex counter_mutex; // 뮤텍스

void safe_increment() { // 스레드에서 실행될 함수

    for (int i = 0; i < 1000; ++i) { 
        // 임계영역
        lock_guard<mutex> lock(counter_mutex); // 락 획득 시 잠김

        shared_counter++; // 하나의 스레드만 이 코드 실행

    }
}

int main() {

    constexpr int NUM_THREADS = 5; // 5개의 스레드 생성
    std::vector<thread> threads;

    for (int i = 0; i < NUM_THREADS; ++i) {
        threads.emplace_back(safe_increment);
    }

    for (auto& t : threads) { // 모든 스레드 작업 완료 대기
        t.join();
    }

    // 예상 최종 값: 5 스레드 * 1000회 = 5000
    int expected_value = NUM_THREADS * 1000;

    std::cout << "예상 값: " << expected_value << std::endl;
    std::cout << "실제 값: " << shared_counter << std::endl;

    if (shared_counter == expected_value) {
        std::cout << "일치" << std::endl;
    }
    else {
        std::cout << "불일치" << std::endl;
    }

    return 0;
}
// constexpr: 값이 반드시 컴파일 시간 상수임을 보장, 컴파일 시간에 값을 계산하고 결정하도록 강제
// constexpr로 선언된 값은 컴파일러가 해당 값을 실제 숫자로 대체 하여 런타임에 계산과정이 아에 사라져 실행 속도가 빨라짐
// constexpr 변수는 주로 읽기 전용 메모리 영역(예: 데이터 세그먼트)에 저장되어 런타임 스택이나 힙 메모리를 사용하지 않으므로 효율적
// .emplace_back(): 객체를 미리 만들어서 벡터에 추가하는 대신, safe_increment 인자를 받아 벡터 내부에서 직접 std::thread 객체를 생성
// join(): join()이 호출된 대상 스레드가 작업을 완료하고 종료될 때까지 기다리도록 지시하는 함수
```
