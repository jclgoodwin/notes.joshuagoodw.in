---
layout: page
title: ARTS (Autumn)
fulltitle: Analysable Real-Time Systems
---


* [Module website](http://www-module.cs.york.ac.uk/arts/ARTS_Part_1.html)


## Introduction

Real-time system
: "Any information processing system which has to respond to externally generated input stimuli within a finite and specified period". 
   Correctness may depend on _when_ the result is delivered, not just on _what_ result is delivered

Embedded [computer] system
: Where a computer "is a component in a larger engineering system". 99% of all processors are for this market, apparently

Hard real-time
: "Where it is absolutely imperative that responses occur within the required deadline"

Soft real-time
: Deadlines important, but system can still function if deadlines are missed sometimes

Firm real-time
: Like soft, but where late delivery of service is as bad as no delivery at all

Time-aware
: "System makes explicit reference to time"

Reactive
: Deadline measured from time of input

Time-triggered
: "Triggered by the passage of time". Might be **periodic**

Event-triggered
: Triggered by external or internal event. **Sporadic** if there's a bound on the event's _arrival interval_, **aperiodic** otherwise


## Computational models

### Control loop

    loop
      read from sensors (in)
      out := G(S,in)
      S := F(S,in)
      write to actuators (out)
    end loop

`S`
: Internal state, an arbitrary data structure

`G` and `F`
: Arbitrary complex functions

Sampling frequency
: How often the environmental quantity must be sampled. Determined by the environmental quantity's nature

Input jitter
: Bound on variability of sampling frequency

Period, T
: The reciprocal of the sampling frequency, and a more popular way of specifying it

Deadline, D
: Maximum time between start of loop and completion of output

Output jitter
: Constraint on output being produced too early

Worst-case execution time (WCET), C