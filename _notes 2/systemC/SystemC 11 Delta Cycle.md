---
title: SystemC 11 Delta Cycle
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 11 Delta Cycle

---

# Delta Cycle

A delta cycle can be thought of as a **very small step of time within the simulation**, which **does not increase the user-visible time**.
A delta cycle is comprised of **separate evaluate and update phases**, and multiple delta cycles may occur at a particular simulated time. 
When a **signal assignment** occurs, other processes **do not see the newly assigned value until the next delta cycle**.

# When to use delta cycle

1. `notify(SC_ZERO_TIME)` causes the event to be notified in the evaluate phase of the next delta cycle, this is called a "**delta notification**".
2. A (direct or indirect) call to `request_update()` causes the `update()` method to be called in the update phase of the current delta cycle.

Note: Multiple `wait(SC_ZERO_TIME)` proceeds the simulated time by multiple delta cycles.

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(DELTA) {
  int x = 1, y = 1; // defines two member variables
  SC_CTOR(DELTA) {
    SC_THREAD(add_x); // x += 2
    SC_THREAD(multiply_x); // x *= 3
    SC_THREAD(add_y); // y += 2
    SC_THREAD(multiply_y); // y *= 3
  }
  void add_x() { // x += 2 happens first
    std::cout << "add_x: " << x << " + 2" << " = ";
    x += 2;
    std::cout << x << std::endl;
  }
  void multiply_x() { // x *= 3 happens after a delta cycle
    wait(SC_ZERO_TIME);
    std::cout << "multiply_x: " << x << " * 3" << " = ";
    x *= 3;
    std::cout << x << std::endl;
  }
  void add_y() { // y += 2 happens after a delta cycle
    wait(SC_ZERO_TIME); 
    std::cout << "add_y: " << y << " + 2" << " = ";
    y += 2;
    std::cout << y << std::endl;
  }
  void multiply_y() { // y *=3 happens first
    std::cout << "multiply_y: " << y << " * 3" << " = ";
    y *= 3;
    std::cout << y << std::endl;
  }
};

int sc_main(int, char*[]) {
  DELTA delta("delta");
  sc_start();
  return 0;
}

// Result:

//because of the delta_cycle, x and y goes through different orders of arithmetic calculation, achieving different results.
//x += 2 happens first
add_x: 1 + 2 = 3
//y *= 3 happens first
multiply_y: 1 * 3 = 3
//(y*3) + 2 = 5
add_y: 3 + 2 = 5
//(x+2) * 3 = 9
multiply_x: 3 * 3 = 9
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


