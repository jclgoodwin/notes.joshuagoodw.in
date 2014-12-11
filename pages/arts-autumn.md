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

(Lectures 2 and 3)


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


### Task interactions and safety

Recall the "state", `S`, from the control loop mentioned above.
This state may be shared with other computations, which complicates matters.

Separate computations must wait to access the state.
This must be done properly: we don't want a tight-deadline computation to be waiting for one that has a long one.


### The Ravenscar profile/model

One name for the model we have seen. Later we'll see how Ada supports it. For sporadics, it is inadequate.


### Cyclic executives

One approach to scheduling. Happen to be **completely deterministic**, which is cool.

"The design is concurrent but the code is produced as a collection of procedures."
Are are no actual "tasks" at runtime -- just procedure calls.

Set of "minor cycles" mapped onto a major cycle.

"The procedures share a common [memory] address space" so can easily pass data between themselves.
Concurrent access is not possible, so data protection isn't needed.

#### Limitations

* "All periods must be a multiple of the minor cycle time"
* Difficult to incorporate tasks with long T
* Impossible to incorporate sporadics
* Diffucult to support flexible scheduling methods
* Construction is NP-hard
* In practice, a real program wold have to be split into fixed-sized procedures
  -- a structure that may not make sense "from a software engineering perspective"
* Determinism less important than _predictability_

...and presumably this approach is incompatible with multiprocessor systems.




## Scheduling real-time systems

[(Lectures 4 and 5)](http://www-course.cs.york.ac.uk/arts/Lect4and5.pdf)



### Scheduling

Two general parts:

* Algorithm to order the use of system resources (especially the CPU)
* Way to predict worst-case behaviour of system when that algorithm is applied

#### Task-based scheduling

Recall the properties of task-based execution.

* Tasks exist at runtime
* Each task is either:
  * Runnable (i.e. probably running),
  * Suspended waiting for a _timing event_, or
  * Suspended waiting for a _non-timing event_


### Scheduling approaches

#### Fixed-priority scheduling

* Widely used in real world, so we will emphasise it here
* Each task has a **fixed, static priority** (computed before runtime)
  - derived only from its temporal requirements (as in deadline-monotonic proirity assigment, explained below)
* Order of execution is determined (in some way) by the priority

#### Earliest deadline first

* Dynamic
  * Because **absolute deadline** not known before runtime
  * Absolute deadline = relative deadline + release time
* Execution is determined by absolute deadline

#### Least laxity

* Dynamic
* Laxity = deadline − work left to do

In practice, if context-switching has any overhead at all, and laxity is rechecked regularly
... "non-optimal" conditions during system overload.

#### Value-based scheduling

More adaptive than static priorities or simple deadlines.

#### Preemption

What happens when a higher-priority task is released while a lower-priority one is executing?

* In a **preemptive scheme**, the higher-priority task is switched to immediately
  - Preferred because higher-priority tasks can be more reactive
* In a **non-preemptive scheme**, the lower-priority task is allowed to complete first 
* **Cooperative dispatching** (or **deferred preemption**) is a "halfway house"...

Various scheduling schemes can have preemptive and non-preemptive variants.


### Necessary and sufficient tests

Necessary
: Where a test failure means deadlines will be missed

Sufficient
: Where a test pass means deadlines will be met

Exact
: Where a test is both necessary and sufficient


### Rate monotonic priority assignment

* Each task has a unique priority
* A task with a shorter period must have 

We say this is optimal -- any [task-set that can be scheduled using preëmptive priority-based scheduling with a fixed-priority assignment scheme] can be scheduled with a rate monotonic assignment sheme. (I.e. it's as good as any fixed-priority assignment scheme.)


### Utilisation-based analysis

* Sufficient but not necessary (sort of pessimistic)
  * An apparently unschedulable task set may in practice be just fine, then
* Only works for D=T task sets

As N approaches infinity, the "utilisation bound" -- the maximum jek utilisation -- approaches 0.693 (from above).

$$
U
=
\sum ^N _{i=1} \frac{C_i}{T_i} \leq N (2^{ \frac{1}{N} } - 1)
$$


### Response time--based analysis

Response time = computation time + interference from other (higher- or equal-priority) tasks

The important equation:

$$
R_i
=
C_i
+
\sum _{j \in hp(i)} \left\lceil \frac{R_i}{T_j} \right\rceil C_j
$$

$$hp(i)$$ is the set of tasks with priority ≥ that of task i. The strange brackets denote a **ceiling function**. The appearance of $$R_i$$ on both sides of the equation suggests a **recurrence relation**.

#### Example

Task-set stolen from the tutorials worksheet (exercise 5), and prioritised deadline-monotonically (which is an optimal ordering):

 Task |  T  |  C  | Priority
------|-----|-----|----------
   Q  |  10 |  2  | 4
------|-----|-----|----------
   S  |  12 |  6  | 3
------|-----|-----|----------
   V  |  20 |  6  | 1
------|-----|-----|----------
   Z  |  30 |  4  | 2

Start with the highest-priority task, Q, which experiences no interference:

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
  w^0_S =
  6 + \left\lceil \frac { 0 }{ 10 } \right\rceil 2
  &= 6
\\
  w^1_S =
  6 + \left\lceil \frac { 6 }{ 10 } \right\rceil 2
  &= 8
\\
  w^2_S =
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
  R_Z = C_Z + &
  \left\lceil \frac{ R_Z }{ T_Q } \right\rceil C_Q +
  \left\lceil \frac{ R_Z }{ T_S } \right\rceil C_S
\\
  = 4 + &
  \left\lceil \frac{ R_Z }{ 10 } \right\rceil 2 +
  \left\lceil \frac{ R_Z }{ 30 } \right\rceil 6
\\
  w^0_Z = 4 + &
  \left\lceil \frac { 0 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 0 }{ 30 } \right\rceil 6
  = 4
\\
  w^1_Z = 4 + &
  \left\lceil \frac { 4 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 4 }{ 30 } \right\rceil 6
  = 12
\\
  w^2_Z = 4 + &
  \left\lceil \frac { 12 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 12 }{ 30 } \right\rceil 6
  = 14
\\
  w^3_Z = 4 + &
  \left\lceil \frac { 14 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 14 }{ 30 } \right\rceil 6
  = 14
\\
  \therefore R_Z
  = 14
\\

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
\\
  w^0_V = 6 + &
  \left\lceil \frac { 0 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 0 }{ 30 } \right\rceil 6 +
  \left\lceil \frac { 0 }{ 12 } \right\rceil 4
  = 6
\\
  w^1_V = 6 + &
  \left\lceil \frac { 6 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 6 }{ 30 } \right\rceil 6 +
  \left\lceil \frac { 6 }{ 12 } \right\rceil 4
  = 18
\\
  w^2_V = 6 + &
  \left\lceil \frac { 18 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 18 }{ 30 } \right\rceil 6 +
  \left\lceil \frac { 18 }{ 12 } \right\rceil 4
  = 24
\\
  w^3_V = 6 + &
  \left\lceil \frac { 24 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 24 }{ 30 } \right\rceil 6 +
  \left\lceil \frac { 24 }{ 12 } \right\rceil 4
  = 26
\\
  w^4_V = 6 + &
  \left\lceil \frac { 26 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 26 }{ 30 } \right\rceil 6 +
  \left\lceil \frac { 26 }{ 12 } \right\rceil 4
  = 30
\\
  w^5_V = 6 + &
  \left\lceil \frac { 30 }{ 10 } \right\rceil 2 +
  \left\lceil \frac { 30 }{ 30 } \right\rceil 6 +
  \left\lceil \frac { 30 }{ 12 } \right\rceil 4
  = 30
\\
  \therefore R_Z
  = 30
\end{split}
$$

Note that for the tutorial question, most of this working-out was not required
-- it was quickly obvious that the response time of S (8 ms) exceeds its deadline (7 ms)!

Response time analysis is both a necessary and a sufficient test -- that is to say, it is an exact test.




## Extending the task model

(Lecture 6)




## Priority ceiling protocols

(Lecture 7)




## Further extending the model

(Lectures 8, 9 and 10)




## EDF scheduling

(Lecture 11)




## WCET analysis

(Lecture 12)

So far, worst-case execution times have been conveniently provided.
Where do they come from?

Turns out timing analysis is a big, hard problem.
The unsolvability of the halting problem could suggest it's impossible!
Needless to say, we'll only scratch the surface here.


### Measurement

A fool's errand. 


### Static analysis


## 2014 exam

1.  1.  "Describe the three scheduling schemes: EDF (Earliest Deadline First),
        FP (Fixed Priority) and LL (Least Laxity).
        For each give the utilisation bound for schedulability of a task set
        with implicit deadlines on a single processor system."

        EDF: tasks executed in order of absolute deadline (known only at runtime), shortest D first.

        FP: each task assigned a static, fixed priority before runtime, and executed in that order.

        LL: tasks executed in order of laxity, least L first, where laxity = deadline − remaining computation time. (Dynamic...)

    2.  


## 2013 exam
