---
layout: article
title: Branch Instruction Prediction
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
- Processor pipelines
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
/*
 * pattern {String} A pattern of branch predictions, i.e. 'NTNTTNTT' where 'N' is not-taken and 'T' is taken
 */
function processPattern(pattern) {
  var steps = pattern.toUpperCase().split(''),
      table = 'Pass\tHist\t\tPred\tActual\n${results}\n\n',
      results = [],
      pass = 1, hist = 0,
      pred, outc, correct;
      
  for (var p = 1; p <= pass; p++) {
    for (var i = 0; i < steps.length; i++) {
      // make the prediction, capture the outcome, and see if the prediction is correct
      pred = (hist) ? 'T' : 'N';
      outc = steps[i];
      correct = (pred === outc);
      // push the prediction results into our output array
      results.push([p, hist, pred, outc, correct].join('\t\t'));
      // update the history
      hist = (outc === 'T') ? 1 : 0;
    }
    
    return table.replace(/\${results}/, results.join('\n'));
  }
}
``` 
The above code implements a simple 1-bit counter. It performs great if the branch behavior is always taken or always 
not-taken. It's very efficient for loops. Any other type of pattern, and this predictor fails miserably. 

So, the plan, as mentioned before, is to implement additional types of predictors which perform better at predicting 
more random types of behavior. The general rule is if you have a pattern that repeats every *n*th time, then you need 
a predictor with at least an _n_+1 history. The user will have a choice of which type of predictor to use and compare 
results.

This ends part one of this blog. I'll return to it to detail how I plan to tackle processor pipelines. Unitl then, 
thanks for reading!
