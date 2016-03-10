---
layout: article
title: High Performance Computer Architecture
---

I enrolled in the Masters program at the Georgia Institute of Technonlogy (Georgia Tech) back in 
August 2015. In that time, I've completed a class in Computational Photography and I'm currently taking 
High Performance Computer Architecture. I was proud of the final project I developed for the Computational 
Photography class, but I can't really share its implementation because of academic integrity reasons.
However, in my High Performance Computer Architecture class, there are no coding projects you develop from
scratch - it's mostly refactoring existing code and running software simulations on emulated hardware.   

> I'm a visual and tactile learner, so, once I've understood a technical computing problem through 
reading, a need to implement that problem's solution wells up in me.

This is where I got the idea to start a project that developed some ideas introduced in class, so that 
I could grasp a better understanding of the topic's implementations. The simulation projects were 
interesting, but I was more interested in the processes of indexing data, moving instructions through a 
pipeline, and decomposing processes into simple actions. I'm a visual and tactile learner, so, once I've 
understood a technical computing problem through reading, a need to implement that problem's solution 
wells up in me. There were a few ideas I had which I thought would make good exercises because they were 
interesting and not trivial to implement. These ideas include: 

- Branch prediction
- Dynamic instruction scheduling
- Processor caches

## Branch Prediction

With this idea, I don't want to get too complicated. I'd like to keep it simple by only reporting results
of predictors with _n_-bit counters and predictors with _n_-bit histories and 2-bit saturating counters. 

The first step is to map out the relationships between branch behavior and an _n_-bit counter. If a 
branch is not taken, we decement the counter. If the branch is taken, we increment it. Simple. The next 
step is then to map out how branch history affects prediction. For every bit of history, there are two
_n_-bit counters. So, if I have two bits of history, then I'll have four counters to update, potentially.
 
Saturating counters are typically used in tandem with histories. The term "saturating" simply means
that the counter changes prediction to another behavior only after that behavior has occured a specific 
number of times. For example, if a 2-bit counter starts at `00`, it will predict not-taken until two taken 
branches occur. Only at that point will it predict taken.

Here is a trivial example of how such a branch predictor would function in code:

```javascript
(function() {
  
})();
``` 

## Dynamic Scheduling

The problem here is to implement [Tomasulo's algorithm](https://en.wikipedia.org/wiki/Tomasulo_algorithm) 
with a reorder buffer.

## Processor Caches

With processor caches, the process I'd like to emulate is the usage of an L1 cache for processor 
instructions that reports hits and misses, sets valid and dirty bits, tags cache lines, and reads or 
writes data.