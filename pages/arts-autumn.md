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

~~~
loop
  read from sensors (in)
  out := G(S,in)
  S := F(S,in)
  write to actuators (out)
end loop
~~~

`S`
: Internal state, an arbitrary data structure

`G` and `F`
: Arbitrary complex functions

Sampling frequency
: How often the environmental quantity must be sampled. Determined by the environmental quantity's nature

Input jitter
: Bound on variability of sampling frequency  (`read from sensors (in) within 2ms`)

Period, T
: The reciprocal of the sampling frequency, and a more popular way of specifying it

Deadline, D
: Maximum time between start of loop and completion of output (`loop every 25ms` [...] `write to actuators (out) within 15ms`)

Output jitter
: Constraint on output being produced too early (`write to actuators (out) after 12ms`)

Worst-case execution time (WCET), C
: Obvious, right?


### Event loop

For event-triggered activities.

~~~
loop
  wait for event (in)
  out := G(S,in)
  S := F(S,in)
  write to actuators (out)
end loop
~~~

Deadline
: Measured from time of event

Period, T
: Bound on how often the event can be fired? (Simplest bound: say there is a minimum time interval between events)

#### Timed event loop

~~~
loop at most every T
  wait for event (in)
  out := G(S,in)
  S := F(S,in)
  write to actuators (out) within D
end loop
~~~

### Sporadic events

Where the event source has a **minimum arrival interval**.

### Aperiodic events

Where the event source _doesn't_ have a minimum arrival interval.

#### Aperiodic loop

~~~
loop with (10ms every 50ms)
  wait for event (in)
  S := F(S,in)
  out := G(S,in)
  write to actuators (out) within 30ms
end loop
~~~

## Task interactions

Recall the "state", `S`, from the control loop mentioned above.
This state may be shared with other computations, which complicates matters.

Separate computations must wait to access the state.
This must be done properly: we don't want a tight-deadline computation to be waiting for one that has a long one.

### Cyclic executives

One approach to scheduling. Happen to be **completely deterministic**, which is cool.

"The design is concurrent but the code is produced as a collection of procedures."









## A response time calculation

The important equation:

$$
R_i
=
C_i
+
\sum _{j \in hp(i)} \left\lceil \frac{R_i}{T_j} \right\rceil C_j
$$

$$hp(i)$$ is the set of tasks with priority ≥ that of task i. The strange brackets denote a **ceiling function**. The appearance of $$R_i$$ on both sides of the equation suggests a **recurrence relation**.

### Computing the actual answer

Let's _do_ an example. I nicked this task-set from the tutorials worksheet, and prioritised it deadline-monotonically (which is an optimal ordering):

 Task |  T  |  C  | Priority
------|-----|-----|----------
   Q  |  10 |  2  | 4
------|-----|-----|----------
   S  |  12 |  6  | 3
------|-----|-----|----------
   V  |  20 |  6  | 1
------|-----|-----|----------
   Z  |  30 |  4  | 2

Start with the highest-priority task, Q, because it's the easiest:

$$
\begin{gathered}
  hp(Q) = \emptyset
\\
  \therefore R_Q = C_Q = 2
\end{gathered}
$$

Then S (priority 3) ($$ hp(Z) = \{Q\}$$):

$$
\begin{aligned}
  R_S
  = C_S &+ \left\lceil \frac{ R_S }{ T_Q } \right\rceil C_Q
\\
  = 6 &+ \left\lceil \frac{ R_S }{ 10 } \right\rceil 2
\end{aligned}
$$

Solve the recurrance relation:

$$
\begin{aligned}
  6 + 1 \times 2
  &= 8
\\
  6 + \left\lceil \frac { 8 }{ 10 } \right\rceil 2
  &= 8
\\
  \therefore R_S
  &= 8
\end{aligned}
$$

Then Z (priority 2) ($$ hp(Z) = \{Q, S\}$$):

$$
\begin{aligned}
  R_Z
  = C_Z + &
  \left\lceil \frac{ R_Z }{ T_Q } \right\rceil C_Q +
  \left\lceil \frac{ R_Z }{ T_S } \right\rceil C_S
\\
  = 4 + &
  \left\lceil \frac{ R_Z }{ 10 } \right\rceil 2 +
  \left\lceil \frac{ R_Z }{ 30 } \right\rceil 6
\end{aligned}
$$

Then V (priority 1) ($$hp(V) = \{Q, S, Z\}$$):

$$
\begin{aligned}
  R_V
  = C_V + &
  \left\lceil \frac{ R_V }{ T_Q } \right\rceil C_Q +
  \left\lceil \frac{ R_V }{ T_S } \right\rceil C_S +
  \left\lceil \frac{ R_V }{ T_Z } \right\rceil C_Z
\\
  = 6 + &
  \left\lceil \frac{ R_V }{ 10 } \right\rceil 2 +
  \left\lceil \frac{ R_V }{ 30 } \right\rceil 6 +
  \left\lceil \frac{ R_V }{ 12 } \right\rceil 4
\end{split}
$$


