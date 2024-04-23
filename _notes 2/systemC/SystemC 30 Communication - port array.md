---
title: SystemC 30 Communication - port array
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 30 Communication - port array

---

When declaring a port:
1. the **first** argument is the **name of an interface** proper, which is also the **type of the port**:
	- a port can only be **bound to a channel** derived from the type of the port or to another port or export with a type derived from the type of the port.
2. the **second** argument is an optional integer value that specifies the **maximum number of channel instances** to which the port instance may be bound:
	- a) the default value is 1.
	- b) if the value is zero, the port may be bound to an arbitrary number of channel instances.
	- c) it's an error to bind a port to more channel instances than the number permitted.
3. the **third** argument is an optional port policy of type `sc_port_policy` that determines the rules for binding multiports and the rules for unbound ports:
	- a) `[default] SC_ONE_OR_MORE_BOUND`: the port shall be bound to one or more channels, the maximum number being determined by the value of the second argument. It's an error for the port to remain unbound at the end of elaboration.
	- b) `SC_ZERO_OR_MORE_BOUND`: the port shall be bound to zero or more channels, the maximum number being determined by the value of the second argument. The port may remain unbound at the end of elaboration.
	- c) `SC_ALL_BOUND`: the port shall be bound to exactly the number of channel instances given by value of the second argument, no more and no less, provided that value is greater than zero.
		- If the value of the second argument is **zero**, policy `SC_ALL_BOUND` has the same meaning as policy `SC_ONE_OR_MORE_BOUND`.
		- It's an error for the port to remain unbound at the end of elaboration, or to be bound to fewer channel than the number required by the second argument.

It's an error to bind a given port to a given channel more than once, whether directly or through another port.

Another way to define port array is using the C/C++ array syntax: 
- `sc_port<IF> p[10]` or 
- `vector<sc_port<IF>> p(10);`

# Example

```cpp
1. sc_port<IF>    // Bound to exactly 1 channel instance
2. sc_port<IF,0>  // Bound to 1 or more channel instances, with no upper limit
3. sc_port<IF,3>  // Bound to 1, 2, or 3 channel instances
4. sc_port<IF,0,SC_ZERO_OR_MORE_BOUND> // Bound to 0 or more channel instances, with no upper limit
5. sc_port<IF,1,SC_ZERO_OR_MORE_BOUND> // Bound to 0 or 1 channel instances
6. sc_port<IF,3,SC_ZERO_OR_MORE_BOUND> // Bound to 0, 1, 2, or 3 channel instances
7. sc_port<IF,3,SC_ALL_BOUND>          // Bound to exactly 3 channel instances
8. sc_port<IF> p[3]                      // an array of 3 ports, each binds to exactly 1 channel instance
9. vector<sc_port<IF>> p(3)            // an array of 3 ports, each binds to exactly 1 channel instance
```

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
#include <vector> // used to define a vector of ports
using namespace sc_core;

SC_MODULE(WRITER) {
  sc_port<sc_signal_out_if<int>> p1; // #1: exactly 1 channel
  sc_port<sc_signal_out_if<int>, 0> p2; // #2: 1 or more channels, no upper limit
  sc_port<sc_signal_out_if<int>, 3> p3; // #3: 1, 2, or 3 channels
  sc_port<sc_signal_out_if<int>, 0, SC_ZERO_OR_MORE_BOUND> p4; // #4: 0 or more channels, no upper limit
  sc_port<sc_signal_out_if<int>, 1, SC_ZERO_OR_MORE_BOUND> p5; // #5: 0 or 1 channels
  sc_port<sc_signal_out_if<int>, 3, SC_ZERO_OR_MORE_BOUND> p6; // #6: 0, 1, 2, or 3 channels
  sc_port<sc_signal_out_if<int>, 3, SC_ALL_BOUND> p7; // #7: exactly 3 channels
  std::vector<sc_port<sc_signal_out_if<int>>> p9; // #9: vector of port
  SC_CTOR(WRITER) : p9(3) { // init p9 to size of 3
    SC_THREAD(writer);
  }
  void writer() {
    int v = 1;
    while (true) {
      p9[0]->write(v); // write to p9[0]
      p7[1]->write(v++); // write to p7[1]
      wait(1, SC_SEC);
    }
  }
};
SC_MODULE(READER) {
  sc_port<sc_signal_in_if<int>> p1; // #1: exactly 1 channel
  sc_port<sc_signal_in_if<int>, 0> p2; // #2: 1 or more channels, no upper limit
  sc_port<sc_signal_in_if<int>, 3> p3; // #3: 1, 2, or 3 channels
  sc_port<sc_signal_in_if<int>, 0, SC_ZERO_OR_MORE_BOUND> p4; // #4: 0 or more channels, no upper limit
  sc_port<sc_signal_in_if<int>, 1, SC_ZERO_OR_MORE_BOUND> p5; // #5: 0 or 1 channels
  sc_port<sc_signal_in_if<int>, 3, SC_ZERO_OR_MORE_BOUND> p6; // #6: 0, 1, 2, or 3 channels
  sc_port<sc_signal_in_if<int>, 3, SC_ALL_BOUND> p7; // #7: exactly 3 channels
  std::vector<sc_port<sc_signal_in_if<int>>> p9; // #9: exactly 3 channels
  SC_CTOR(READER) : p9(3) { // init p9 to size of 3
    SC_THREAD(reader7);
    sensitive << p7; // sensitive to any element of port array p7
    dont_initialize();
    SC_THREAD(reader9);
    sensitive << p9[0] << p9[1] << p9[2]; // sensitive to any element of port array p9
    dont_initialize();
  }
  void reader7() {
    while (true) {
      std::cout << sc_time_stamp() << "; reader7, port 0/1/2 = " << p7[0]->read() << "/" << p7[1]->read() << "/" << p7[2]->read() << std::endl;
      wait();
    }
  }
  void reader9() {
    while (true) {
      std::cout << sc_time_stamp() << "; reader9, port 0/1/2 = " << p9[0]->read() << "/" << p9[1]->read() << "/" << p9[2]->read() << std::endl;
      wait();
    }
  }
};

int sc_main(int, char*[]) {
  WRITER writer("writer"); // instantiate writer
  READER reader("reader"); // instantiate reader
  // declare channels
  sc_signal<int> s1; // 1 channel
  std::vector<sc_signal<int>> s2(10); // 10 channels
  std::vector<sc_signal<int>> s3(2); // 2 channel
  // leave s4 un-bound
  sc_signal<int> s5; // 1 channel
  std::vector<sc_signal<int>> s6(2); // 2 channels
  std::vector<sc_signal<int>> s7(3); // 3 channels
  // #8 is same as #9, omitted
  std::vector<sc_signal<int>> s9(3); // 3 channels
  // bind ports
  writer.p1(s1); // #1
  reader.p1(s1); // #1
  for (unsigned int i = 0; i < s2.size(); ++i) { // #2
    writer.p2(s2[i]);
    reader.p2(s2[i]);
  }
  for (unsigned int i = 0; i < s3.size(); ++i) { // #3
    writer.p3(s3[i]);
    reader.p3(s3[i]);
  }
  // s4 un-bound
  writer.p5(s5); // #5
  reader.p5(s5); // #5
  for (unsigned int i = 0; i < s6.size(); ++i) { // #6
    writer.p6(s6[i]);
    reader.p6(s6[i]);
  }
  for (unsigned int i = 0; i < s7.size(); ++i) { // #7
    writer.p7(s7[i]);
    reader.p7(s7[i]);
  }
  for (unsigned int i = 0; i < s9.size(); ++i) { // #9
    writer.p9[i](s9[i]);
    reader.p9[i](s9[i]);
  }
  sc_start(2, SC_SEC);
  return 0;
}

// Result:
// p9[0] has value
0 s; reader9, port 0/1/2 = 1/0/0
// p7[1] has value
0 s; reader7, port 0/1/2 = 0/1/0
// p9[0] updated
1 s; reader9, port 0/1/2 = 2/0/0
//p7[1] updated
1 s; reader7, port 0/1/2 = 0/2/0
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)

