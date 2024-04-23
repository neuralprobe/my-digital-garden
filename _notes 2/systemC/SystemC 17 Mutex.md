---
title: SystemC 17 Mutex
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 17 Mutex

---


![[Pasted image 20230615150245.png]]

# Mutex
1. is a predefined **channel** intended to model the behavior of a **mutual exclusion lock** used to control access to a resource shared by concurrent processes.
2. shall be in one of two exclusive states: **unlocked or locked**
	- a) only one process can lock a given mutex at one time.
	- b) a mutex can only be unlocked by the process that locked it. After being unlocked, a mutex may be locked by a different process.

# Member functions of Mutex:
1. `int lock()`:
	- a) If the mutex is unlocked, `lock()` shall lock the mutex and return.
	- b) If the mutex is locked, `lock()` shall suspend until the mutex is unlocked (by another process). $\rightarrow$ So, it's a spinning lock
	- c) If multiple processes attempt to lock the mutex in the same delta cycle, the choice of which process instance is given the lock shall be non-deterministic.
	- d) shall **unconditionally return the value 0**.
2. `int trylock()`:
	- a) If the mutex is unlocked, `trylock()` shall lock the mutex and shall return the value 0. (0 means success)
	- b) If the mutex is locked, `trylock()` shall immediately return the value `–1`. The mutex shall remain locked. (-1 means failure)
3. `int unlock():`
	- a) If the mutex is unlocked, `unlock()` shall return the value –1. The mutex shall remain unlocked. (-1 means failure)
	- b) If the mutex was locked by a process instance other than the calling process, `unlock()` shall return the value –1. The mutex shall remain locked.
	- c) If the mutex was locked by the calling process, member function unlock shall unlock the mutex and shall return the value 0.  (0 means success)
	- immediate notification shall be used to signal the act of unlocking a mutex to other processes

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MUTEX) {
  sc_mutex m;
  SC_CTOR(MUTEX) {
    SC_THREAD(thread_1);
    SC_THREAD(thread_2);
  }
  void thread_1() {
    while (true) {
      if (m.trylock() == -1) { // try to lock the mutex
        m.lock(); // failed, wait to lock
        std::cout << sc_time_stamp() << ": thread_1 obtained resource by lock()" << std::endl;
      } else { // succeeded
        std::cout << sc_time_stamp() << ": thread_1 obtained resource by trylock()" << std::endl;
      }
      wait(1, SC_SEC); // occupy mutex for 1 s
      m.unlock(); // unlock mutex
      std::cout << sc_time_stamp() << ": unlocked by thread_1" << std::endl;
      wait(SC_ZERO_TIME); // give time for the other process to lock the mutex
    }
  }
  void thread_2() {
    while (true) {
      if (m.trylock() == -1) { // try to lock the mutex
        m.lock(); // failed, wait to lock
        std::cout << sc_time_stamp() << ": thread_2 obtained resource by lock()" << std::endl;
      } else { // succeeded
        std::cout << sc_time_stamp() << ": thread_2 obtained resource by trylock()" << std::endl;
      }
      wait(1, SC_SEC); // occupy mutex for 1 s
      m.unlock(); // unlock mutex
      std::cout << sc_time_stamp() << ": unlocked by thread_2" << std::endl;
      wait(SC_ZERO_TIME); // give time for the other process to lock the mutex
    }
  }
};

int sc_main(int, char*[]) {
  MUTEX mutex("mutex");
  sc_start(4, SC_SEC);
  return 0;
}

// Result:
// thread_1 locks mutex on first try
0 s: thread_1 obtained resource by trylock()
// thread_1 releases mutex
1 s: unlocked by thread_1
// thread_2 locks after thread_1 releases mutex 
1 s: thread_2 obtained resource by lock()
// thread_2 releases mutex
2 s: unlocked by thread_2
// thread_1 locks again
2 s: thread_1 obtained resource by lock()
// thread_1 unlocks
3 s: unlocked by thread_1
// thread_2 locks
3 s: thread_2 obtained resource by lock()

```


---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


