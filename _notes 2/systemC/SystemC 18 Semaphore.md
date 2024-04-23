---
title: SystemC 18 Semaphore
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 18 Semaphore

---


![[Pasted image 20230615150245.png]]

# Semaphore

1. is a predefined **channel** intended to model the behavior of a software semaphore used to provide **limited concurrent access to a shared resource**.
2. has an integer value, **the semaphore value**, which is set to the permitted number of concurrent accesses when the semaphore is constructed.
a) if the initial value is one, the semaphore is equivalent to a **mutex**.

# Member functions of semaphore

1. `int wait()`
	- a) If the semaphore value is greater than 0, `wait()` shall decrement the semaphore value and return.
	- b) If the semaphore value is equal to 0, `wait()` shall suspend until the semaphore value is incremented (by another process).
	- c) Shall unconditionally return the value 0.
1. `int trywait()`
	- a) If the semaphore value is greater than 0, `trywait()` shall decrement the semaphore value and shall return the value 0.
	- b) If the semaphore value is equal to 0, trywait() shall immediately return the value `–1` without modifying the semaphore value. (-1 means failure)
2. `int post()`
	- a) shall increment the semaphore value.
	- b) shall use immediate notification to signal the act of incrementing the semaphore value to any waiting processes.
	- c) shall unconditionally return the value 0.
3. `int get_value()`: 
	- shall return the semaphore value.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(SEMAPHORE) {
  sc_semaphore s; // declares semaphore
  SC_CTOR(SEMAPHORE) : s(2) { // init semaphore with 2 resources
    SC_THREAD(thread_1); // register 3 threads competing for resources
    SC_THREAD(thread_2);
    SC_THREAD(thread_3);
  }
  void thread_1() {
    while (true) {
      if (s.trywait() == -1) { // try to obtain a resource
        s.wait(); // if not successful, wait till resource is available
      }
      std::cout<< sc_time_stamp() << ": locked by thread_1, value is " << s.get_value() << std::endl;
      wait(1, SC_SEC); // occupy resource for 1 s
      s.post(); // release resource
      std::cout<< sc_time_stamp() << ": unlocked by thread_1, value is " << s.get_value() << std::endl;
      wait(SC_ZERO_TIME); // give time for the other process to lock
    }
  }
  void thread_2() {
    while (true) {
      if (s.trywait() == -1) { // try to obtain a resource
        s.wait(); // if not successful, wait till resource is available
      }
      std::cout<< sc_time_stamp() << ": locked by thread_2, value is " << s.get_value() << std::endl;
      wait(1, SC_SEC); // occupy resource for 1 s
      s.post(); // release resource
      std::cout<< sc_time_stamp() << ": unlocked by thread_2, value is " << s.get_value() << std::endl;
      wait(SC_ZERO_TIME); // give time for the other process to lock
    }
  }
  void thread_3() {
    while (true) {
      if (s.trywait() == -1) { // try to obtain a resource
        s.wait(); // if not successful, wait till resource is available
      }
      std::cout<< sc_time_stamp() << ": locked by thread_3, value is " << s.get_value() << std::endl;
      wait(1, SC_SEC); // occupy resource for 1 s
      s.post(); // release resource
      std::cout<< sc_time_stamp() << ": unlocked by thread_3, value is " << s.get_value() << std::endl;
      wait(SC_ZERO_TIME); // give time for the other process to lock
    }
  }
};

int sc_main(int, char*[]) {
  SEMAPHORE semaphore("semaphore");
  sc_start(4, SC_SEC);
  return 0;
}

//Result:
//scheduling pattern: 1&2, then 2&3, then 1&2, then 2&3
//thread_1 locks @ 0 s
0 s: locked by thread_1, value is 1
//thread_2 locks @ 0 s
0 s: locked by thread_2, value is 0
//thread_1 unlocks @ 1 s
1 s: unlocked by thread_1, value is 1
//thread_2 unlocks @ 1 s
1 s: unlocked by thread_2, value is 2
//thread_3 locks @ 1 s
1 s: locked by thread_3, value is 1
//thread_2 locks @ 1 s
1 s: locked by thread_2, value is 0
2 s: unlocked by thread_3, value is 1
2 s: unlocked by thread_2, value is 2
//thread_1 locks @ 2 s
2 s: locked by thread_1, value is 1
//thread_2 locks at 2 s
2 s: locked by thread_2, value is 0
3 s: unlocked by thread_1, value is 1
3 s: unlocked by thread_2, value is 2
//thread_3 locks @ 3 s
3 s: locked by thread_3, value is 1
//thread_2 locks @ 3 s
3 s: locked by thread_2, value is 0
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


