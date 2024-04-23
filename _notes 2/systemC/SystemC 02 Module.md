---
title: SystemC 02 Module
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 02 Module

---


![[Pasted image 20230615124728.png]]

# A systemC module is

1. the smallest container of functionality with state, behavior, and structure for hierarchical connectivity.
2. a C++ class, which inherits a systemC basic class: `sc_module.`
3. the principle structural building block of SystemC.
4. used to represent a component in real systems.

# How to define a systemC module

1. `SC_MODULE(module_name) {}`: this uses the systemC defined macro "`SC_MODULE`", which is equivalent to \#2.
2. `struct module_name: public sc_module {}`: a struct that inherits `sc_module`.
3. `class module_name : public sc_module { public: }`: a class that inherits `sc_module`.

Note, `class` is identical to `struct` except for its default access control mode of "private", as compared to "public" of struct.

# How to use a systemC module

1. Objects of class ==`sc_module`== can only be ==constructed during elaboration==. It shall be an error to instantiate a module during simulation.
2. Every class derived (directly or indirectly) from `sc_module` shall have ==at least one constructor==. Every constructor shall have ==one and only one parameter of class `sc_module_name`== but may have further parameters of classes other than `sc_module_name`.
3. A ==string-valued argument== shall be passed to the constructor of every module instance. It is good practice to make this string name the same as the C++ variable name through which the module is referenced, if such a variable exists.
4. (To be explained later) ==Inter-module communication== should typically be accomplished using ==interface method calls==; that is, a module should communicate with its environment through its ports. Other communication mechanisms are permissible, for example, for debugging or diagnostic purposes.


# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MODULE_A) { // approach #1, use systemC provided SC_MODULE macro
  SC_CTOR(MODULE_A) { // default constructor
    std::cout << name() << " constructor" << std::endl; // name() returns the object name, which is provided during instantization
  }
};
struct MODULE_B : public sc_module { // approach #2, this uses c++ syntax and is more readiable
  SC_CTOR(MODULE_B) {
    std::cout << name() << " constructor" << std::endl;
  }
};
class MODULE_C : public sc_module { // approach #3, use class instead of struct
public: // have to explicitly declare constructor function as public 
  SC_CTOR(MODULE_C) {
    std::cout << name() << " constructor" << std::endl;
  }
};

int sc_main(int, char*[]) { // systemC entry point
  MODULE_A module_a("module_a"); // declare and instantiate module_a, it's common practice to assign module name == object name
  MODULE_B module_b("modb"); // declare and instantiate module_b, module name != object name
  MODULE_C module_c("module_c"); // declare and instantiate module_c
  sc_start(); // this can be skipped in this example because module instantiation happens during elaboration phase which is before sc_start
  return 0;
}

// Result
module_a constructor
modb constructor
module_c constructor
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)