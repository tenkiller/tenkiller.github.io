---
layout: article
title: Dynamic Instruction Scheduling
---

## Dynamic Scheduling

During my high-performance computer architecture class at [Georgia Tech](http://omscs.gatech.edu), we discussed data hazards, such as, read-after-write. This particular hazard refers to the situation when an instruction refers to a result that has not yet completed being processed. For example, take a look at the following instrcutions:

```assembly
MUL R1,R1,R2
ADD R4,R1,2
```

The multiply instruction is writing its result to register `R1` and immediately following that the add instruction is reading `R1`. If the multiply instruction has not written to `R1` by the time the add instruction reads it, then the program will be reading erroneous data and the program will not perform correctly. This is a true data dependency and we must take measures to ensure the program performs as intended.

Register renaming is a simple way to solve this problem, and [Tomasulo's algorithm](https://en.wikipedia.org/wiki/Tomasulo_algorithm) is the elegant way to do it. Its power is in its simplicity. All that's required is few data structures built into hardware. These are the instruction queue (IQ), architectural register file (ARF), register alias table (RAT), reservation station (RS), reorder buffer (ROB), and common data bus (CDB). See the figure below for a logical view of how these pieces fit together. In fact, you can implement Tomasulo's algorithm without a reorder buffer, but you won't be able to handle program exceptions, like divide-by-zero, that may occur because of a bug in the code or some other reason.

[logical view image placeholder]
