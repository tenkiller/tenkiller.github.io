---
layout: article
title: A Processor Pipeline
image:
  teaser: stainless_pipes.jpg
---

> This exercise is simply a means to gain a deeper understanding into how these things function.

Processor pipelines are complicated. In-order processors that aren't truely pipelined are simple to implement, but, I'm 
talking about processors that fetch, decode, and execute instructions, access memory, and write to registers *in 
parallel*. As I began to think about this problem and implement a simple solution, its complexity soon became apparent. 
Let me show you a trivial example of what I was attempting to do.

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

Even though this implementation works, there are obvious problems. First, it does not execute instructions in parallel. 
An instruction is fetched and processed through the pipeline completely before the next instruction is fetched. What 
needs to happen is that an instruction is fetched on every iteration (cycle). Secondly, there are no checks for 
dependencies between instructions. So, if an instruction depends on the outcome of a previous instruction, it won't wait 
to execute.

A naive way to solve the first problem is to implement the functions of the pipeline as promises. This way we can just 
"fire and forget" each step. However, that isn't parallel programming. These operations are still synchronous. We need 
to be able to execute each function on the same cycle. I sort of have to live with this limitation, given my chosen 
language. But that's ok. Like I said before, this exercise is simply a means to gain a deeper understanding into how 
these things function. To solve the second problem, a smart choice is to implement dynamic instruction scheduling, 
&aacute; la [Tomasulo's algorithm](https://en.wikipedia.org/wiki/Tomasulo_algorithm). That's a topic for a future post.

### A Better Implementation (But Still Flawed)

> Now that's throughput baby!

After a hours of thought, here's a better implementation of my processor pipeline. It's not perfect, but it at least
performs multiple stages of the pipeline within the same cycle. My original idea to use promises could possibly still 
work, but I went with a more direct approach using callbacks. I also decided to rewrite it in 
[ES2015](http://www.ecma-international.org/ecma-262/6.0/) because I needed the practice and I enjoy the new features it 
has.

```javascript
class Pipeline {
  constructor(instructions) {
    this.instructions = instructions;
    this.stack = [];
    this.cycle = 1;
    this.log = [];
  }
  
  fetch(cycle) {
    // fetch the instruction
    let instruction = this.instructions.shift();
        
    this.stack.push({ cycle: cycle+1, func: this.decode, args: [instruction] });
    this.log.push(`${cycle} : FETCHED instruction "${instruction}"`);
  }
  
  decode(cycle, [instruction]) {    
    if (!instruction) {
      throw new ReferenceError('No instruction to decode.');
    }
    
    // interpret instruction opcode and operands
    let parts = instruction.split(' '),
        opcode = parts[0],
        operands = parts[1].split(',');
    
    this.stack.push({ cycle: cycle+1, func: this.execute, args: [instruction, opcode, operands] });
    this.log.push(`${cycle} : DECODED instruction into opcode "${opcode}" and operands "${operands.join(',')}"`);
  }
  
  execute(cycle, [instruction, opcode, operands]) {    
    if (!opcode || !operands) {
      throw new ReferenceError('No opcode or operands to execute.');
    }
    // perform calculations on data and memory addresses
    this.stack.push({ cycle: cycle+1, func: this.memory, args: [instruction] });    
    this.log.push(`${cycle} : EXECUTED "${opcode}" on "${operands.slice(1).join(',')}"`);
  }
  
  memory(cycle, [instruction]) {
    // load and store data to memory
    this.stack.push({ cycle: cycle+1, func: this.write, args: [instruction] });
    this.log.push(`${cycle} : MEMORY accessed for "${instruction}"`);
  }
  
  write(cycle, [instruction]) {
    // write results to registers
    this.log.push(`${cycle} : WRITE performed for "${instruction}"`);
  }
  
  run() {    
    while (true) {
      // if there's an instruction, fetch it
      if (this.instructions.length) {
        this.stack.push({ cycle: this.cycle, func: this.fetch });
      }
      
      // get only the steps in the pipeline stack that execute this cycle
      let steps = this.stack.filter((obj) => {
        return obj.cycle === this.cycle;
      });
      
      // if there's nothing to execute, we're done
      if (!steps.length) {
        this.log.push('--DONE--');
        break;
      }
      
      // run the pipeline steps for this cycle
      while (steps.length) {
        let step = steps.shift();
        step.func.call(this, step.cycle, step.args);
      }
    
      this.cycle++;
    }
    
    return this.log;
  }
}

export default Pipeline;
```

It's not a lot of code, but allow me to explain how it works in a little more detail. A list of instructions is passed
to the pipeline when its instantiated. The pipeline is very particular about how the instructions are formatted, but I
decided to skip validation in favor of getting a working implementation running first. When the pipeline is run, it 
enters an "endless" loop. The only way out of it is if we don't have any steps in the pipeline to process during a given 
cycle.

If there's an instruction in the queue, we push an object onto our execution stack. This object contains a few useful
properties that we'll need when we want to execute it later. Let's call this object a "step". A step has three 
properties: 1) the cycle it will execute on, 2) the function in the pipeline to execute, 3) and any arguments needed by
the function. If the function doesn't need any arguments, it's omitted. 

Each function in a step, when executed, adds a new step to the execution stack. For example, if you look at `fetch()`, 
it creates a step that executes the `decode()` function on `cycle+1` with `instruction` as an argument. The `cycle+1` is
pretty important. We control which steps execute based on the cycle number. We need to make sure added steps execute on 
a future cycle because time travel is not possible in our simulated hardware. When we go through another iteration of 
our run loop, we filter out only the steps on the stack whose cycle match the current processing cycle, i.e. 
`this.cycle`. The expected outcome is if we have five or more instructions to process, we'll have cycles where all three
pipeline functions are executing. Now that's throughput baby!

As soon as we've run out of steps on the stack to execute, we break out of the loop and return the log as output. I'm 
pretty pleased with this naive implementation I threw together, but, by no means is it realistic or infallible. There 
are plenty of other ways to do this (and probably faster and more efficiently), but it was a fun exercise and I already 
have a list of improvements I'd like to make.
