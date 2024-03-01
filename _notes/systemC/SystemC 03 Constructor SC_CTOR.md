---
title: SystemC 03 Constructor SC_CTOR
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---

# SystemC 03 Constructor SC_CTOR

---

Every c++ function must have a constructor. For a normal c++ function, a default constructor will be auto-generated if not explicitly provided.
However, **every systemC module** must have a **unique "name"**, which is provided when instantiating a module object. This requires **a constructor with at least one parameter**.

SystemC provides a macro (`SC_CTOR`) for convenience when declaring or defining a constructor of a module. 

# SC_CTOR

1. shall only be used where the rules of C++ permit a constructor to be declared and can be used as the declarator of a constructor declaration or a constructor definition.
2. has **only one argument**, which is the **name of the module class** being constructed.
3. **cannot add user-defined arguments** to the constructor. If an application needs to pass additional arguments, the constructor shall be provided explicitly.

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MODULE_A) {
  SC_CTOR(MODULE_A) { // constructor taking only module name
    SC_METHOD(func_a); // register member function to systemC simulation kernel, to be explained later.
  }
  void func_a() { // a member function with no input, no output
    std::cout << name() << std::endl;
  }
};

SC_MODULE(MODULE_B) {
  SC_CTOR(MODULE_B) { // constructor
    SC_METHOD(func_b); // register member function
  }
  void func_b(); // declare function
};
void MODULE_B::func_b() { // define function outside class definition
  std::cout << this->name() << std::endl;
}
SC_MODULE(MODULE_C) { // constructor taking more arguments
  const int i;
  SC_CTOR(MODULE_C); // SC_HAS_PROCESS is recommended, see next example for details
  MODULE_C(sc_module_name name, int i) : sc_module(name), i(i) { // explcit constructor
    SC_METHOD(func_c);
  }
  void func_c() {
    std::cout << name() << ", i = " << i << std::endl;
  }
};

int sc_main(int, char*[]) {
  MODULE_A module_a("module_a");
  MODULE_B module_b("module_b");
  MODULE_C module_c("module_c",1);
  sc_start();
  return 0;
}

// Result
module_a
module_b
module_c i = 1
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)