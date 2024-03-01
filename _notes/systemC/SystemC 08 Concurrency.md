---
title: SystemC 08 Concurrency
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 08 Concurrency

---

#  Simulated concurrency

SystemC uses simulation processes to model concurrency. It's not true concurrent execution.

When multiple processes are simulated as running concurrently, **only one is executed at a particular time**. However, the **simulated time remain unchanged** until all concurrent processes finishes their current tasks.

Thus, these processes are running **concurrently on the same "simulated time"**. This differs from e.g. the Go language, which is real concurrency.

Let's understand the simulated concurrency with a simple example.

# Code
```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(CONCURRENCY) {
  SC_CTOR(CONCURRENCY) { // constructor
    SC_THREAD(thread1); // register thread1
    SC_THREAD(thread2); // register thread2
  }
  void thread1() {
    while(true) { // infinite loop
      std::cout << sc_time_stamp() << ": thread1" << std::endl;
      wait(2, SC_SEC); // trigger again after 2 "simulated" seconds
    }
  }
  void thread2() {
    while(true) {
      std::cout << "\t" << sc_time_stamp() << ": thread2" << std::endl;
      wait(3, SC_SEC);
    }
  }
};

int sc_main(int, char*[]) {
  CONCURRENCY concur("concur"); // define an object
  sc_start(10, SC_SEC); // run simulation for 10 seconds
  return 0;
}


// Result

//thread1 is running
0 s: thread1
	//thread2 is running
	0 s: thread2
//thread1 is running
2 s: thread1
	//thread2 is running
	3 s: thread2
//thread1 is running
4 s: thread1
	//thread2 is running
	6 s: thread2
//thread1 is running
6 s: thread1
//thread1 is running
8 s: thread1
	//thread2 is running
	9 s: thread2
// simulation ends after 10 simulated seconds

```







---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


