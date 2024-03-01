---
title: SystemC 00 Basic
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 00 Basic

---

# SystemC Class Library Structure

![[Pasted image 20230615124247.png]]

# SystemC Terminology

|Terms   | Description  |
|---|---|
|Method|A C++ method, i.e. a member function of a class.|
|[Module](https://www.learnwithexamples.com/basic/module)|A structural entity, which can contain processes, ports, channels, and other modules. Modules allow expressing structural hierarchy. Module is the principle structural building block of SystemC, used to repsent a component in real systems.|
|[Process](https://www.learnwithexamples.com/basic/simu_process)|A special kind of member function of a sc_module class, registered to the SystemC simulation kernel and called only by the simulation Kernel.|
|Interface|An interface provides a set of method declarations, but provides no method implementations and no data fields.|
|[Channel](https://www.learnwithexamples.com/basic/signal_readwrite)|A channel implements one or more interfaces, and serves as a container for communication functionality.|
|[Port](https://www.learnwithexamples.com/basic/port)|A port is an object through which a module can access a channel’s interface. But modules can also access a channel’s interface directly.|
|[Event](https://www.learnwithexamples.com/basic/event)|A process can suspend on, or be sensitive to, one or more events. Events allow for resuming and activating processes.|
|[Sensitivity](https://www.learnwithexamples.com/basic/sensitivity)|The sensitivity of a process defines when this process will be resumed or activated. A process can be sensitive to a set of events. Whenever one of the corresponding events is triggered, the process is resumed or activated.|

---

# SystemC Hierarchy and Components

![[Pasted image 20230615125528.png]]

![[Pasted image 20230609102909.png]]
- Module A is a top level module (component). It has two sub-modules (module b and module c), a channel (c1), and a simulation process (P). Module A also has one input port (p0) and two output ports (p4, p7).
- Module A receives input via port p0, and writes output via ports p4 and p7
- Module b has two simulation processes (X and Y), an event (e), an input port (p1), and two output ports (p2, p3)
- Within module b, processes X and Y are connected through event e.
- Module b connected to module A via ports p0 and p1.
- Module b also connects to process P of module A via port p3.
- Module c has two simulation processes (M and N), an input port (p5), and an output port (p6)
- Within module c, processes M and N are connected through channel c.
- Module b is connected to module c via channel c1 (ports p2 and p5 bound to c1).
- Module c connected to module A via ports p6 and p7.

---

# SystemC Execution Stages

![[Pasted image 20230609102942.png]]

- `sc_main() `is the entrying point of an SystemC application. Before and after `sc_start()` is normal C++ code.
- Upon reaching `sc_start()`, SystemC simulation kernel takes over the scheduling of processes. It returns until a specified simulation time has elapsed, no more actions pending, or `sc_stop()` is called by any of the process.


---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)
- [EE382M.20: System-on-Chip (SoC) Design, Lecture 3 – The SystemC Language by Andreas Gerstlauer](http://users.ece.utexas.edu/~gerstl/ee382m_f18/lectures/lecture_3.pdf)
