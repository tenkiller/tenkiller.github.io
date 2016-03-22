---
layout: article
title: A Processor Pipeline
---

> Like I said before, this exercise is simply a means to gain a deeper understanding into how these things function.

Processor pipelines are complicated. In-order processors that aren't truely pipelined are simple to implement, but, I'm talking about
processors that fetch, decode, and execute instructions, access memory, and write to registers *in parallel*. As I began to think about this problem and implement a simple solution, its complexity soon became apparent. Let me show you a trivial example of what I was attempting to do.

### The Trivial Example

```javascript
(function() {
  var Pipeline = function(instructionQueue) {
    this.queue = instructionQueue;
    this.log = [];
  };
  
  Pipeline.prototype.fetch = function() {
    // fetch instruction from the queue
    this.instruction = this.queue.shift();
    this.log.push('FETCHED instruction "' + this.instruction + '"');
  };
  
  Pipeline.prototype.decode = function() {
    // interpret instruction opcode and operands
    if (!this.instruction) {
      throw new ReferenceError('No instruction to decode.');
    }
    
    var parts = this.instruction.split(' ');
    this.opcode = parts[0];
    this.operands = parts[1].split(',');
    
    this.log.push('DECODED instruction into opcode "' + this.opcode + 
                  '" and operands "[' + this.operands.join(',') + ']"');
  };
  
  Pipeline.prototype.execute = function() {
    // perform calculations on data and memory addresses
    if (!this.opcode || !this.operands) {
      throw new ReferenceError('No opcode or operands to execute.');
    }
    
    this.log.push('EXECUTED "' + this.opcode + '" on "[' + this.operands.slice(1).join(',') + ']"');
  };
  
  Pipeline.prototype.memory = function() {
    // load and store data to memory
    this.log.push('MEMORY accessed for "' + this.instruction + '"');
  };
  
  Pipeline.prototype.write = function() {
    // write results to registers
    this.log.push('WRITE performed to "[' + this.operands[0] + ']" for "' + this.instruction + '"');
  };
  
  Piepline.prototype.run = function() {
    while(this.queue.length) {
      this.fetch();
      this.decode();
      this.execute();
      this.memory();
      this.write();
    }
    
    console.log(this.log.join('\n'));
  };
  
  var instructions = ['ADD R1,R2,1', 'SUB R3,R4,2'],
      pipeline = new Pipeline(instructions);
      
  pipeline.run();
})();
```

Even though this implementation works, there are obvious problems. First, it does not execute instructions in parallel. An instruction is fetched and processed through the pipeline completely before the next instruction is fetched. What needs to happen is that an
instruction is fetched on every iteration (cycle). Secondly, there are no checks for dependencies between instructions. So, if an
instruction depends on the outcome of a previous instruction, it won't wait to execute.

One way to solve the first problem is to implement the functions of the pipeline as promises. This way we can just "fire and forget"
each step; however, there will be no synchronicity between operations. I can live with that, because I'm not actually trying to 
implement a full-featured simulation of a processor pipeline. Like I said before, this exercise is simply a means to gain a deeper
understanding into how these things function.

### A Better Implementation (But Still Flawed)
