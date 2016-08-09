---
layout: article
title: Dynamic Instruction Scheduling
---

## Dynamic Scheduling

During my high-performance computer architecture class at [Georgia Tech](http://omscs.gatech.edu), we discussed data 
hazards, such as, read-after-write. This particular hazard refers to the situation when an instruction refers to a 
result that has not yet completed being processed. For example, take a look at the following instrcutions:

```assembly
MUL R1,R1,R2
ADD R4,R1,2
```

The multiply instruction is writing its result to register `R1` and immediately following that the add instruction is 
reading `R1`. If the multiply instruction has not written to `R1` by the time the add instruction reads it, then the 
program will be reading erroneous data and the program will not perform correctly. This is a true data dependency and 
we must take measures to ensure the program performs as intended.

Register renaming is a simple way to solve this problem, and 
[Tomasulo's algorithm](https://en.wikipedia.org/wiki/Tomasulo_algorithm) is the elegant way to do it. Its power is in 
its simplicity. All that's required are a few data structures built into hardware. These are the instruction queue (IQ), 
architectural register file (ARF), register alias table (RAT), reservation station (RS), reorder buffer (ROB), and 
common data bus (CDB). See the figure below for a logical view of how these pieces fit together. In fact, you can 
implement Tomasulo's algorithm without a [reorder buffer](https://en.wikipedia.org/wiki/Re-order_buffer), but you won't 
be able to handle program exceptions or rollback control to a target instruction due to a branch misprediction.

[logical view image placeholder]

Let's explore how the entire process should work. We first fetch an instruction from the IQ and parse it into its opcode 
and operands. The opcode will tell us the type of instruction being performed and how long it will take to complete 
execution. Next, we look to see if we have a free reservation station slot and a free reorder buffer slot. It's 
important to note that _both_ need to have a free slot, not one or the other. If both slots are free, we may continue. 

Adding the instruction to the RS and ROB is a simple procedure. First, the instruction opcode and destination register 
in the ARF are written to the ROB. Second, the instruction opcode and ROB entry we just filled in are written to the RS. 
Additionally, the instruction operands are referenced in the RAT, and their values are written to the RS. It's important 
to note that each entry in the RAT corresponds to a single register in the ARF. If the RAT entry is empty, then it 
returns the value of its corresponding ARF register. Finally, the entry in the RAT that corresponds to the destination 
register of the instruction is written with the value of the ROB entry we wrote to previously. This way, any future 
instruction that refers to this current instruction's destination register will lookup the value in the ROB entry, not 
the ARF.

After the instruction is added to the RS and ROB, and an update to the RAT has been made, we make a check to see if it 
can be executed this cycle. In order to execute an instruction during the current cycle, it must have values for each of 
its operands. In other words, if an operand is still waiting on the return value from a previous instruction (i.e. it 
still points to a ROB entry), then the current instruction cannot execute. Once its operands contain a value, and it 
begins execution, it may then be dispatched to the executuon units over the CDB and freed from the RS. After execution 
of the instruction completes, the return value is written to the ROB entry and the ROB entry is marked as done. Entries 
in the ROB only get freed if it is marked as done and the ROB commit pointer is pointing to it in the current cycle. The 
commit pointer basically walks through each ROB entry, one at a time, _in order_, and writes their value to the 
destination register in the ARF that the ROB entry points to. This entire process cycle repeats until all of the 
instructions have been executed.
