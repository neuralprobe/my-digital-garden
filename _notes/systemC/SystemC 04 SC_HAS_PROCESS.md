---
title: SystemC 04 SC_HAS_PROCESS
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 04 SC_HAS_PROCESS

---

# SC_CTOR vs SC_HAS_PROCESS

`SC_HAS_PROCESS` is introduced since systemC v2.0. It takes **only one argument** which is the **name of the module class**. It is often compared with **SC_CTOR**. Let's see how these two macros are defined:

1. `SC_SCOR`: `#define SC_CTOR(user_module_name) typedef user_module_name SC_CURRENT_USER_MODULE; user_module_name( ::sc_core::sc_module_name )`
2. `SC_HAS_PROCESS`: `#define SC_HAS_PROCESS(user_module_name) typedef user_module_name SC_CURRENT_USER_MODULE`

When providing e.g. "module" as input argument to `SC_CTOR` and `SC_HAS_PROCESS`, they expand to:
1. `SC_CTOR(module)`: `typedef module SC_CURRENT_USER_MODULE; module( ::sc_core::sc_module_name )`
2. `SC_HAS_PROCESS(module)`: `typedef module SC_CURRENT_USER_MODULE;`

From this you can see:
1. both defines the "`module`" as "`SC_CURRENT_USER_MODULE`", which is used when **registering member functions to simulation kernel** via `SC_METHOD/SC_THREAD/SC_CTHREAD.`
2. `SC_CTOR` also declares a default constructor with module name as the only input argument. The impact is:
	a) `SC_CTOR` saves one line of code to write a constructor file, whereas if using `SC_HAS_PROCESS`, one has to declare a constructor function:
	`module_class_name(sc_module_name name, additional argument ...);`
	b) since `SC_CTOR` has a constructor function declaration, it can only be placed inside class header.

# When to use SC_HAS_PROCESS

My recommendation:
1. ==Don't use `SC_CTOR` or `SC_HAS_PROCESS` if a module has no simulation process (member functions registered to simulation kernel via `SC_METHOD/SC_THREAD/SC_CTHREAD`)==
2. ==Use `SC_CTOR` if the module need no additional parameter (other than module name) to instantiate==
3. ==Use `SC_HAS_PROCESS` when additional parameters are needed during instantiation==

![[Pasted image 20230615125706.png]]

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MODULE_A) { // module without simulation processes doesn't need SC_CTOR or SC_HAS_PROCESS
  MODULE_A(sc_module_name name) { // c++ style constructor, the base class is implicitly instantiated with module name.
    std::cout << this->name() << ", no SC_CTOR or SC_HAS_PROCESS" << std::endl;
  }
};

SC_MODULE(MODULE_B1) { // constructor with module name as the only input argument
  SC_CTOR(MODULE_B1) { // implicitly declares a constructor of MODULE_B1(sc_module_name)
    SC_METHOD(func_b); // register member function to simulation kernel
  }
  void func_b() { // define function
    std::cout << name() << ", SC_CTOR" << std::endl;
  }
};

SC_MODULE(MODULE_B2) { // constructor with module name as the only input argument
  SC_HAS_PROCESS(MODULE_B2); // no implicit constructor declarition
  MODULE_B2(sc_module_name name) { // explicit constructor declaration, also instantiate base class by default via sc_module(name)
    SC_METHOD(func_b); // register member function
  }
  void func_b() { // define function
    std::cout << name() << ", SC_HAS_PROCESS" << std::endl;
  }
};

SC_MODULE(MODULE_C) { // pass additional input argument(s)
  const int i;
  SC_HAS_PROCESS(MODULE_C); // OK to use SC_CTOR, which will also define an un-used constructor: MODULE_A(sc_module_name);
  MODULE_C(sc_module_name name, int i) : i(i) { // define the constructor function
    SC_METHOD(func_c); // register member function
  }
  void func_c() { // define function
    std::cout << name() << ", additional input argument" << std::endl;
  }
};

SC_MODULE(MODULE_D1) { // SC_CTOR inside header, constructor defined outside header
  SC_CTOR(MODULE_D1);
  void func_d() {
    std::cout << this->name() << ", SC_CTOR inside header, constructor defined outside header" << std::endl;
  }
};
MODULE_D1::MODULE_D1(sc_module_name name) : sc_module(name) { // defines constructor. Fine with/without "sc_module(name)"
  SC_METHOD(func_d);
}

SC_MODULE(MODULE_D2) { // SC_HAS_PROCESS inside header, constructor defined outside header
  SC_HAS_PROCESS(MODULE_D2);
  MODULE_D2(sc_module_name); // declares constructor
  void func_d() {
    std::cout << this->name() << ", SC_CTOR inside header, constructor defined outside header" << std::endl;
  }
};
MODULE_D2::MODULE_D2(sc_module_name name) : sc_module(name) { // defines constructor. Fine with/without "sc_module(name)"
  SC_METHOD(func_d);
}

SC_MODULE(MODULE_E) { // SC_CURRENT_USER_MODULE and constructor defined outside header
  MODULE_E(sc_module_name name); // c++ style constructor declaration
  void func_e() {
    std::cout << this->name() << ", SC_HAS_PROCESS outside header, CANNOT use SC_CTOR" << std::endl;
  }
};
MODULE_E::MODULE_E(sc_module_name name) { // constructor definition
  SC_HAS_PROCESS(MODULE_E); // NOT OK to use SC_CTOR
  SC_METHOD(func_e);
}

int sc_main(int, char*[]) {
  MODULE_A module_a("module_a");
  MODULE_B1 module_b1("module_b1");
  MODULE_B2 module_b2("module_b2");
  MODULE_C module_c("module_c", 1);
  MODULE_D1 module_d1("module_d1");
  MODULE_D2 module_d2("module_d2");
  MODULE_E module_e("module_e");
  sc_start();
  return 0;
}

// Result

module_a, no SC_CTOR or SC_HAS_PROCESS
module_b1, SC_CTOR
module_b2, SC_HAS_PROCESS
module_c, additional input argument
module_d1, SC_CTOR inside header, constructor defined outside header
module_d2, SC_CTOR inside header, constructor defined outside header
module_e, SC_HAS_PROCESS outside header, CANNOT use SC_CTOR

```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)