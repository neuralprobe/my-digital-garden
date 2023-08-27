---
title: Event Driven Simulator
date: 2023-08-27
tags: C++ OOP Simulator Simulation
---

# Event-Driven Simulator

이벤트-기반 시뮬레이터는 시뮬레이터에 포함된 한 모듈이 ... 
- 특정 시간에 어떤 (자기 자신이나 다른) 모듈의 제어에 의해, 특정한 동작을 하게 되는데 
- 이를 동작을 통해 모듈의 상태를 바꾸고, 특정 시간을 소비하고, 또 다른 모듈과 상호작용하는 과정을 시간의 흐름 가운데 모사할 수 있는 프로그램이다.

이를 구현하기 위해 이벤트-기반 시뮬레이터는 ...
- Event를 특정 시간에 notify/schedule 해서 특정 모듈에 정의된 Process를 trigger 시키고,
- Process에서 특정 모듈에 포함된 멤버함수들이 실행되면서 해당 모듈의 멤버 변수를 업데이트 하거나, 또 다른 event notify/schedule 해서 다른 모듈과의 상호작용을 표현할 수 있다.

따라서 이벤트-기반 시뮬레이터를 짜는데 가장 중요한 부분은 ...
- Event 클래스: 
	- 특정 시간에 특정 함수 (글로벌 함수나 특정 객체의 멤버함수)를 실행시켜야함. 
	- 따라서, 시간을 정보를 담을 `int time` 변수와,
	- callable 객체를 담을 수 있는 `std::function<void()> action;` 변수를 멤버 변수로 갖고 있어야 한다. 
		- 하나의 이벤트에 의해 여러개의 함수를 부르고 싶으면 `std::vector` 를 이용하자
	- Event queue 에 이벤트를 담을 때, 시간 순서대로 자동으로 정렬이 되도록 `operator<()` 멤버함수를 이용해서 이벤트의 priority가 시간의 선후로 결정될 수 있도록 해주자.
- Event 를 담을 수 있는 priority queue:
	- 시뮬레이터는 내부에 있는 여러 모듈이 상호작용하기 때문에 수없이 많은 이벤트가 스케쥴되고 발생한다. 따라서, 이 많은 이벤트들을 simulation time을 기준으로 정렬하고 이벤트들이 "시간 순서대로" 발생할 수 있도록 관리해주는 데이터 구조가 필요한데, C++의 priority queue 를 사용하면 된다.
	- 즉.. `std::priority_queue<Event> eventQueue;`
- Event  adder:
	- 새로운 이벤트를 이벤트 큐에 담을 때, 그 이벤트가 발생할 시간과 호출될 call-back 함수를 인자로 받아, 이벤트를 생성해서 이벤트 큐에 넣어줄 함수를 만들어 주자.
-  현재 simulation time
	- 이벤트 발생의 순서를 정하고 타겟 시스템의 성능을 알려주기 때문에 가장 중요한 객체다. 
	- 단순히 시뮬레이터 클래스에 `int currentTime;` 으로 만들어두면 됨.
-  run 함수:
	- 이벤트 큐에 등록이 된 이벤트들을 순서대로 실행시켜주는 함수.
	- 하나의 이벤트에 의해 호출되는 함수에서 다른 이벤트를 스케쥴한다면, 이벤트 큐는 채워지고 소비되는 과정을 연속하다가, 특정 조건에서 시뮬레이션을 멈추게 하면 된다.

아래는 Event-driven simulator의 간단한 예시 코드. (chatGPT로 짬(?). 하하)

```cpp
#include <iostream>
#include <queue>
#include <functional>

class EventDrivenSimulator {
private:
    struct Event {
        int time;
        std::function<void()> action;

        bool operator<(const Event& other) const {
            return time > other.time;
        }
    };

    std::priority_queue<Event> eventQueue;
    int currentTime;

public:
    EventDrivenSimulator() : currentTime(0) {}

    void addEvent(int time, std::function<void()> action) {
        eventQueue.push({time, action});
    }

    void run() {
        while (!eventQueue.empty()) {
            Event currentEvent = eventQueue.top();
            eventQueue.pop();

            currentTime = currentEvent.time;
            std::cout << "Current time: " << currentTime << '\n';

            currentEvent.action();
        }
    }
};

int main() {
    EventDrivenSimulator simulator;

    simulator.addEvent(5, []() { std::cout << "Event at time 5\n"; });
    simulator.addEvent(15, []() { std::cout << "Event at time 15\n"; });
    simulator.addEvent(10, []() { std::cout << "Event at time 10\n"; });

    simulator.run();

    return 0;
}

```


