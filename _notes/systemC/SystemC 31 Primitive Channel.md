---
title: SystemC 31 Primitive Channel
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 31 Primitive Channel

---

![[Pasted image 20230615131116.png]]
![[Pasted image 20230615131312.png]]
![[Pasted image 20230615131620.png]]

# sc_prim_channel

1. is the **base class** for **all primitive channels**
2. provides **primitive channels** with unique **access to the update phase** of the scheduler.
3. contains no hierarchy, ports, or simulation processes.
4. in common with hierarchical channels, a primitive channel may provide public member functions that can be called using the interface method call paradigm.

![[Pasted image 20230615150137.png]]
![[Pasted image 20230615150245.png]]
# Member functions in sc_prim_channel

`sc_prim_channel` provides the following member functions:
- a) `request_update()`: 
	- scheduler to queue an update request for the channel
- b) `async_request_update()`: 
	1) scheduler to queue an update request for the channel in **a thread-safe manner**. May be called reliably from an operating system thread other than systemC kernel.
	2) not recommended to be called from functions executed in the context of the systemC kernel
- c) `update()`:
	1) called back by the scheduler during the update phase in response to a call to `request_update` or `async_request_update`.
	2) an application may override this member function. The definition of this function in `sc_prim_channel` itself does nothing.
	3) typically only read and modify data members of the current object and create delta notifications.
	4) shall not:
		- a) call any member function of class `sc_prim_channel` with the exception of member function update itself if overridden within a base class of the current object
		- b) call member function `notify()` of class `sc_event` with no arguments to create an immediate notification
		- c) call any of the member functions of class `sc_process_handle` for process control (suspend or kill, for example)
		- d) change the state of any storage except for data members of the current object.
		- e) read the state of any primitive channel instance other than the current object.
		- f) call interface methods of other channel instances. In particular, member function update should not write to any signals.
		- g) `next_trigger()`
		- h) `wait()`

A channel shall implement one or more interface, thus needs to inherit from an interface class (of base class type `sc_interface`). The interface provides the required methods for the channel.


```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
#include <string>
using namespace sc_core;

class GENERATOR_IF : public sc_interface { // interface for interrupt generator
	public:
		virtual void notify() = 0;
};

class RECEIVER_IF : public sc_interface { // interface for interrupt receiver
	public:
		virtual const sc_event& default_event() const = 0; // needed for sensitive
};

class INTERRUPT : public sc_prim_channel, public GENERATOR_IF, public RECEIVER_IF { // interrupt class
	private:
		sc_event e; // private event for synchronization
	public:
		INTERRUPT(sc_module_name name) : sc_prim_channel(name) {} // constructor, construct sc_prim_channel
		void notify() { // implement GENERATOR_IF
			e.notify();
		}
		const sc_event& default_event() const { // implement RECEIVER_IF
			return e;
		}
};

SC_MODULE(GENERATOR) { // interrupt generator class
	sc_port<GENERATOR_IF> p; // port to generate interrupt
	SC_CTOR(GENERATOR) { // constructor
	    SC_THREAD(gen_interrupt);
	}
	void gen_interrupt() {
		while (true) {
			p->notify(); // calls notify function of the INTERRUPT channel
			wait(1, SC_SEC);
		}
	}
};

SC_MODULE(RECEIVER) { // interrupt receiver class
	sc_port<RECEIVER_IF> p; // port to receive interrupt
	SC_CTOR(RECEIVER) { // constructor
		SC_THREAD(rcv_interrupt);
		sensitive << p; // monitors interrupt on port p
		dont_initialize();
	}
	void rcv_interrupt() { // triggered upon interrupt
		while (true) {
		std::cout << sc_time_stamp() << ": interrupt received" << std::endl;
		wait();
		}
	}
};

int sc_main(int, char*[]) {
  GENERATOR generator("generator"); // instantiate generator
  RECEIVER receiver("receiver"); // instantiate receiver
  INTERRUPT interrupt("interrupt"); // instantiate interrupt
  generator.p(interrupt); // port binding
  receiver.p(interrupt); // port binding
  sc_start(2, SC_SEC);
  return 0;
}

// Result:
// triggered by interrupt event
0 s: interrupt received
1 s: interrupt received
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


