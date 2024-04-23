---
title: SystemC 01 Hello World
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 01 Hello World

---

# Two approaches to print a string to console

  1. ==c++ style==: print from a normal c++ function
  2. ==systemc style==: print from a systemC method invoked by systemC simulation kernel

Before we start, you need to know some basics.

# SystemC header file

To use the SystemC class library features, an application shall include either of the C++ header files specified below:
  1. ==`#include <systemc.h>`==
    a) `systemc.h` add all of the names from the namespaces `sc_core` and `sc_dt` to the declarative region in which it is included, together with the name `sc_unnamed` and selected names from the standard C or C++ libraries (e.g. `cin`, `cout`, `iostream`).
    b) `systemc.h` is provided for backward compatibility with earlier versions of SystemC and may be deprecated in future versions of this standard.
  2. ==`#include <systemc>`==
	  1. `systemc` is the old `systemc.h` without `using`s
	  2. A common practice is to include `<systemc>`, followed by `using namespace sc_core`. The programmer can then include other namespaces on a need basis.

# SystemC entry point
  
- while a normal c++ program's entry point is the `main()` function, systemC user has to use ==`int_sc_main(int argc, char* argv[])`== as the entry point.
- This is because systemC library has the `main()` function already defined. `main()` will call `sc_main()` and passes the command-line parameters.

# SystemC module

- a systemC module is a class (or struct) that inherits the `sc_module` base class. This will be discussed in upcoming examples.

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc> // include the systemC header file
using namespace sc_core; // use namespace

void hello1() { // a normal c++ function
  std::cout << "Hello world using approach 1" << std::endl;
}

struct HelloWorld : sc_module { // define a systemC module
  SC_CTOR(HelloWorld) {// constructor function, to be explained later
    SC_METHOD(hello2); // register a member function to the kernel
  }
  void hello2(void) { // a function for systemC simulation kernel, void inside () can be omitted
    std::cout << "Hello world using approach 2" << std::endl;
  }
};

int sc_main(int, char*[]) { // entry point
  hello1(); // approach #1: manually invoke a normal function
  HelloWorld helloworld("helloworld"); // approach #2, instantiate a systemC module
  sc_start(); // let systemC simulation kernel to invoke helloworld.hello2();
  return 0;
}

// Result
//from hello1()
Hello world using approach 1
//from hello2()
Hello world using approach 2
```




---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)