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


### Types of deadline

Implicit
: Where D = T

Constrained
: Where D ≤ T

Unconstrained
: ...


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

Where the event source has a **minimum arrival interval**. Require D < T ...


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
* Difficult to support flexible scheduling methods
* Construction is NP-hard
* In practice, a real program would have to be split into fixed-sized procedures
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
  - derived only from its temporal requirements (as in deadline- or rate-monotonic priority assignment, explained below)
* Order of execution is determined (in some way) by the priority

(1 is the lowest priority; a higher number means a higher priority.)

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

* In a **preemptive scheme**, the higher-priority task is switched to immediately (upon release)
  - Preferred because higher-priority tasks can be more reactive
* In a **non-preemptive scheme**, the lower-priority task is allowed to complete first 
* **Cooperative dispatching** (or **deferred preemption**) is a "halfway house"...

Various scheduling schemes can have preemptive and non-preemptive variants.


### Necessary and sufficient tests

Necessary
: Where a test failure means deadlines will be missed (is sufficient evidence of unschedulability!)

Sufficient
: Where a test pass means deadlines will be met (no further evidence needed)

Exact
: Where a test is both necessary and sufficient


### Rate monotonic priority assignment

* Each task has a unique priority
* A task with a shorter period must have a higher priority (higher value)

We say this is **optimal** -- any [task-set that can be scheduled using preëmptive priority-based scheduling with a fixed-priority assignment scheme] can be scheduled with a rate monotonic assignment sheme. (I.e. it's as good as any fixed-priority assignment scheme.)


### Utilisation-based analysis

* Sufficient but not necessary (sort of pessimistic)
  * An apparently unschedulable task set may in practice be just fine, then
* Only works for D=T task sets

As N approaches infinity, the "utilisation bound" -- the maximum schedulable utilisation -- approaches 0.693 (from above).

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
(Although, if there were some overheads unaccounted for, maybe it wouldn't be sufficient...)




## Extending the task model

(Lecture 6)


### Sporadic tasks

("Sporadic" seems to have an unclearly-understood meaning in real life ... like "greedy", etc.)

"Sporadics" characterised by a minimum _inter-arrival_ time, and D < T

An annoying thing about sporadics is that their worst-case execution time figures can be higher than their in-practice average execution time
-- events arrive in bursts, and then at other time 


### Execution-time servers

"A virtual resource layer between the set of applications and the processor[(s)] they execute on"
... "both guarantees a certain level of service and ensures that no more resource is allocated than is implied by the 'service contract'".

The server sort of _serves_ what are called _client tasks_.

A server has a budget (capacity) which can be replenished according to what kind of server it is.

#### Periodic server

* Budget is replenished according to replenishment period
* "Following replenishment, client tasks can execute until either the budget is exhausted,
  or there are no longer any runnable tasks"
  - ...at which point the server is _suspended_ until replenishment period comes around

Behave like periodic tasks.

#### Deferrable server

As periodic, but the server is not suspended just because there are no runnable tasks
(client will always be serviced iff there is some budget available).

Behave like periodic tasks too.

#### Sporadic server

Budget "remains indefinitely", replenishment time is relative to arrival time of a client
(client arrives at `t`, replenishment happens at `t + replenishment period`).

Worst-case behaviour is when


## Priority inheritance/ceiling protocols

(Lecture 7)


### Priority inversion

A problem.

"A task is suspended waiting for a lower-priority task to complete some required computation" ["blocked"]
... clearly, something's not right.
"The priority model is, in some sense, being undermined."


### Priority inheritance

A solution.

If task `p` is blocking task `q`, then run `p` with `q`'s priority.


### Calculating 'blocking'

A task has `m` critical sections that can lead to it being blocked
→ the maximum number of times it can be blocked is `m`

Upper bound on task `i`'s blocking time:

$$
B_i = \sum^K_{k=1} C(k)
$$

#### Adding 'blocking' to the response time equation

$$
R_i = C_i + B_i + I_i
$$

...


### Priority ceiling protocols

"A high-priority task can be blocked at most once during its execution by lower-priority tasks"

Prevents:

* Deadlocks
* Transitive blocking

"Mutual[ly] exclusive access to resources is ensured"

#### Original ceiling priority protocol

* Each task has a static default priority
* Each resource has a static ceiling value
  - Defines maximum priority of tasks that use it
* Each task has an additional dynamic priority
  - This is the maximum of its static priority and **any task that it blocks**
* **A task can only lock a resource if its dynamic priority is higher than the ceiling value of any currently locked resource (that the task has not locked itself)**

#### Immediate ceiling priority protocol

* Each task has a static default priority
* Each resource has a static ceiling value
  - Defines maximum priority of tasks that use it
* Each task has an additional dynamic priority
  - This is the maximum of its static priority and **the ceiling values of any resources that it locks**

"A task will only suffer a block at the very beginning of its execution."
After that, "all the resources it needs must be free".
For needed resources not to be free, an equal- or higher-priority task must lock them
-- so the equal- or lower-priority task's execution would be postponed anyway.

_If a lowish-priority task is using a resource that high-priority tasks might want to use,
give the task a higher priority so it can finish using it as quickly as possible._

#### Comparison

* Both...
  - have same worst-case behaviour
  - do enforce mutual exclusion, prevent deadlocks and transitive blocking...
* ICPP...
  - easier to implement, no monitoring of blocking relationships
  - leads to fewer context switches
  - more priority movements (upon every resource use)
* OCPP...
  - harder to implement, monitoring of blocking relationships
  - fewer priority movements (only when blocking occurs)

#### Baker's stack resource policy

"Probably the best scheme for EDF"

Similar to immediate ceiling priority inheritance.

Each task [before runtime?] assigned a **preemption level**; the shorter the relative deadline, the higher the preemption level.
At run-time, resources given ceiling values (maximum preemption values of tasks that can use them).
Released tasks can preempt executing tasks _iff_ they have shorter absolute deadlines, _and_ their preemption level > highest ceiling of any currently locked resources.

Identical results to those of ICPP.




## Further extending the model

(Lectures 8, 9 and 10)


### Release jitter

Where arrival time ≠ release time.

Can apply to periodics and sporadics.

For a sporadic, if response time is to be measured relative to actual release time:

$$
R_i = C_i + B_i + \sum_{j \in hp(i)} \left\lceil \frac{R_i + J_j}{T_j} \right\rceil C_j
$$


### Execution-time servers

([Recall what they are -- see 'extending the model'](#extending-the-task-model))

Periodic and deferrable servers behave like periodic tasks.



### Cooperative scheduling

### Priority assignment -- Audsley

> If task p is assigned the lowest priority and is
> feasible then, if a feasible priority ordering exists
> for the complete task set, an ordering exists with
> task p assigned the lowest priority

Start with, say, deadline monotonic priority assignment (optimal); then introduce a more fancy concept like "importance".


### Not enough levels of priority

> You eventually learn that true priorities are like arms;
> if you think you have more than a couple,
> you're either lying or crazy.

(Merlin Mann. Sadly an irrelevant distraction here.)

If tasks are forced to share a priority, expect them to interfere with each other.


### 'Power-aware' systems

Run the processor at "the lowest speed commensurate with meeting all deadlines"


### Overheads

(We can't pretend our systems exist in a perfect vacuum like in GCSE Physics or something.)

For fixed-priority scheduling (can imagine more overheads associated with dynamic prirorities, of course), these are the main overheads:

* Context switches
* Interrupts (with sporadic task releases)
* Real-time clock overheads (regarding "clock interruptss")



### The fully extended task model

We end up with...

$$
R_i = CS^1 + C_i + B_i + \sum_{j \in hp(i)} \left\lceil \frac{R_i}{T_j} \right\rceil \left( CS^1 + CS^2 + C_j \right)
$$

Response time for task i
= cost of switching to i
+ computation time for i
+ blocking
+ interference, including the costs of further switching to and from task i


###






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

*   Potentially "optimistic" -- was the worst-case path measured?
*   Have to select test data
    *   Does test data trigger longest _execution trace_?
*   Measured all possible execution traces?
*   "Combining WCET of parts" may not "yield the global WCET"
*   Was the processor's initial state the worst-case state?

A fool's errand? May be useful for rough estimates, though.


### Static analysis

*   "Pessimistic"
*   Complicated by modern processors' "cache(s), pipelines, out-of-order execution, branch prediction etc"




## Multiprocessor systems

[(Lecture 13)](http://www-course.cs.york.ac.uk/arts/Lect13.pdf)

Different to single-processor systems, so analysis is different.


###  Global versus partitioned placement

...where _placement_ is the mapping of tasks to processors.

Partitioned
: tasks are statically assigned to partitions before runtime.

Global
: tasks dynamically allocated as they become runnable, can even move between processors during execution

The global scheme requires runtime support, but also has other disadvantages

The **Dhall effect** is a problem with global scheduling where, despite low utilisation, there is an appearance of unschedulability ... for example, see this task-set:

Task | T  | D  | C
-----|----|----|---
  a  | 10 | 10 | 5
  b  | 10 | 10 | 5
  c  | 12 | 12 | 8

On a two-processor system,
global scheduling would
(assuming earliest-deadline-first, or fixed priorities with a typical rate- or deadline-monotonic priority assignment scheme)
allocate _a_ and _b_ each to different processors,
forcing _c_ to then miss its deadline despite the low (< 2) utilisation.
Partitioned scheduling would have the foresight to allocate a and b to the same processor, and c to the other.

Of course, for other kinds of task-set one can imagine how partitioned scheduling is worse:

Task | T  | D  | C
-----|----|----|---
  d  | 10 | 10 | 9
  e  | 10 | 10 | 9
  f  | 10 | 10 | 2

It is impossible to schedule this task-set on two processors with any partitioned scheme
-- one of the tasks must move between processors during execution.
This can be solved with a global scheme.

(In both cases, the third task could be manually split in two, but doesn't count
-- it would effectively give us a different task-set, so is sort of cheating;
and as we've seen with cyclic executives, this kind of manual fiddling is untenable.)


### TkC priority ordering

A way to use global scheduling without the problems of earliest-deadline-first/deadline-monotonic priority orderings.

$$
T - \left( \frac{ M - 1 + \sqrt{ 5M^2 - 6M + 1 } }{ 2M } \right) \times C
$$

(k represents the whole pile of stuff in the brackets. T is replaced by D if it is smaller.)


### Fight!

In general, perhaps global scheduling needs more research.
Or perhaps the overheads (cost of task migration, cost of shared run-queue) are insurmountable.

Partitioning is hard (allocation of tasks to partitions is an NP-hard problem, reducible to bin-packing).
But one handy heuristic is largest "density" ($$\frac{C}{D}$$) first.


### The semi-partitioned (task-splitting) approach 

Mostly partitioned, but allow a small number (= number of processors) of tasks to migrate during runtime

* A compromise, obviously
* Variations for both earliest-deadline-first and fixed-priority ordering
* Maximum task migrations = M − 1



## Mixed criticality systems

(Lecture 14)

### Background

"Increasing tendency to host more than one system on the same platform..."


### Modifying the task model

L is the integrity level of a task.

$$
L_X \gt L_Y \implies C(L_X) \gt C(L_Y)
$$

(Higher integrity tasks assumed to have longer WCET)


### Schedulability test method 1 (Vestal, 2007)

Response time of _some task_ with criticality C  
= WCET of _that task_  
+ sum of what each higher-criticality task's WCET would be if it had that (low) criticality

$$
R_i = C_i(L_i) + \sum _{j \in hp(i)} \left\lceil \frac{R_i}{T_j} \right\rceil C_j(L_i)
$$

#### Example (exercise 11)

Task 3 has the highest criticality (H) and its C(H) is C

$$
\begin{aligned}
  R_{3} &= 1 + \sum _{\j \in hp(i)} \left\lceil \frac{R_1}{T_j} \right\rceil C_j(L_1)
\\
  R_{3} &= 1
\end{aligned}
$$

It meets its deadline (30).

Task 2 has criticality M


### Schedulability test method 2 (Vestal, 2007)



## 2014 exam

(Answer question 1, and 2 or 3. 50 marks each, for a total of 100.)

1.  1.  "Describe the three scheduling schemes: EDF (Earliest Deadline First),
        FP (Fixed Priority) and LL (Least Laxity).
        For each give the utilisation bound for schedulability of a task set
        with implicit deadlines on a single processor system."

        EDF
        : tasks executed in order of absolute deadline (known only at runtime), shortest D first.

        FP
        : each task assigned a static, fixed priority before runtime, and executed in that order.

        LL
        : tasks executed in order of laxity, least L first, where laxity = deadline − remaining computation time. (Dynamic...)

    2.  "Describe the three run-time characteristics..."

        Preemptive
        : when a higher-priority task arrives during the execution of a lower-priority task, switches to the higher-priority immediately.

        Non-preemptive
        : ... the lower-priority task is allowed to finish execution before the higher-priority task is switched to.

        Non-interruptible
        : ...

    3.  Necessary
        : where a response-time analysis test failure indicates that task deadlines will be missed.

        Sufficient
        : where a response-time analysis test pass
        indicates that task deadlines will be met.

    4.  Optimal priority ordering is rate-monotonic:

        Task | C |  D |  T | Priority
        -----|---|----|----|---------
          T1 | 2 | 10 | 10 | **2**
        -----|---|----|----|---------
          T2 | 3 |  7 |  8 | **3**
        -----|---|----|----|---------
          T3 | 4 | 17 | 17 | **1**

        (T2 has the fastest rate so the highest priority)

        Response time analysis:

        $$ R_{T2} = 3 \lt 7 $$

        ... so T2 is meets its deadline. What about T1?

        $$
        \begin{aligned}
          R_{T1} &=
          C_{T1} + \left\lceil \frac{ R_{T1} }{ T_{T2} } \right\rceil C_{T2}
          \\
          &=
          2 + \left\lceil \frac{ R_{T1} }{ 8 } \right\rceil 3
        \end{aligned}
        $$

        Solve recurrence relation:

        $$
        \begin{aligned}
          w^0_{T1} &=
          2 + \left\lceil \frac{ 0 }{ 8 } \right\rceil 3 =
          2
          \\
          w^1_{T1} &=
          2 + \left\lceil \frac{ 2 }{ 8 } \right\rceil 3 =
          5
          \\
          w^2_{T1} &=
          2 + \left\lceil \frac{ 5 }{ 8 } \right\rceil 3 =
          5 \lt 10
        \end{aligned}
        $$

        Finally, T3:

        $$
        \begin{aligned}
          R_{T3}
          &=
          C_{T3} + \left\lceil \frac{ R_{T3} }{ T_{T2} } \right\rceil C_{T2}
          +
          \left\lceil \frac{ R_{T3} }{ T_{T1} } \right\rceil C_{T1}
          \\
          &=
          4 + \left\lceil \frac{ R_{T3} }{ 8 } \right\rceil 3
          +
          \left\lceil \frac{ R_{T3} }{ 17 } \right\rceil 2
        \end{aligned}
        $$

        Solve the recurrence relation:

        $$
        \begin{aligned}
          w^0_{T3}
          &=
          4 +
          \left\lceil \frac{ 0 }{ 8 } \right\rceil 3
          +
          \left\lceil \frac{ 0 }{ 17 } \right\rceil 2
          = 4
          \\
          w^1_{T3}
          &=
          4 +
          \left\lceil \frac{ 4 }{ 8 } \right\rceil 3
          +
          \left\lceil \frac{ 4 }{ 17 } \right\rceil 2
          = 9
          \\
          w^2_{T3}
          &=
          4 +
          \left\lceil \frac{ 9 }{ 8 } \right\rceil 3
          +
          \left\lceil \frac{ 9 }{ 17 } \right\rceil 2
          = 12
          \\
          w^3_{T3}
          &=
          4 +
          \left\lceil \frac{ 12 }{ 8 } \right\rceil 3
          +
          \left\lceil \frac{ 12 }{ 17 } \right\rceil 2
          = 
          12 \lt 17
        \end{aligned}
        $$

        So the task-set is schedulable.

    5.  "If, in the above example, Tasks T1 and T3 are deemed to be of a high
        criticality, and task T2 is of a low criticality, what would be the ideal priority ordering that
        would still lead to a schedulable system?"

        Task | C |  D |  T | Priority
        -----|---|----|----|---------
          T1 | 2 | 10 | 10 | **3**
        -----|---|----|----|---------
          T2 | 3 |  7 |  8 | **1**
        -----|---|----|----|---------
          T3 | 4 | 17 | 17 | **2**

        (T2 given the lowest priority, then T1 and T3 prioritised rate-monotonically?)

2.  1.  "Explain the function of execution-time servers in real-time systems."

        (Not sure exactly what is being asked for -- _function_ different to  _purpose_?)

        * Virtual resource layer between applications and processor(s)
        * Have a capacity budget which is used by client tasks (one at a time) and replenished according to replenishment period
        * Guarantee a certain level of service, and ensure no more resource is allocated than should be...

    2.  "Compare and contrast the _periodic_ server and the _deferrable_ server."

        Both have a budget which is replenished according to the replenishment period.

        Tasks execute following replenishment, until the budget "is exhausted".

        With the periodic server, the server is suspended when there are no runnable tasks or the budget runs out, until the next replenishment.
        The deferrable server is only suspended if the budget runs out -- if there is budget available, client tasks can be served.

3.  1.  priority inversion
        : where a task is _blocked_ by a lower-priority task -- suspended waiting for the lower-priority task
        "to complete some required" computation

        priority inheritance
        : where a task that is _blocking_ a higher-priority task is made to inherit [temporarily]
        the priority of the higher-priority task it is blocking

        immediate priority ceiling inheritance
        : A protocol where resources have a tasks have a static priority ceiling (tasks of priority > this value cannot use the resource) and
        tasks [can inherit this value if it is higher than their static default priority]
        [priorities inherited from resources]

        Baker's stack resource policy
        : ("Probably the best scheme for EDF") Similar to immediate ceiling priority inheritance.
        Each task [before runtime?] assigned a **preemption level**; the shorter the relative deadline, the higher the preemption level.
        At run-time, resources given ceiling values (maximum preemption values of tasks that can use them).
        Released tasks can preempt executing tasks _iff_ they have shorter absolute deadlines, _and_ their preemption level > highest ceiling of any currently locked resources.

    2.  1.  Simple priority inheritance:

        2.  Immediate priority ceiling inheritance: 

    3.  "The worst-case execution time (WCET) of a task must be determined if the
        temporal behaviour of a system is to be determined. What are the two main methods
        available to obtain WCET values?"

        Measurement and static analysis...

        "What are the benefits and drawbacks of these methods?
        What features of modern processors make estimating WCET difficult?"

        Measurement is likely to be optimistic.
        It can be useful for a rough estimate of the WCET.
        It is difficult to be sure that the processor is in the worst-case initial state,
        that the longest execution trace has been triggered,
        that rare execition scenarios haven't been missed...

        Static analysis is more pessimistic, but it is more complicated thanks to features of modern processors such as caches, branch prediction, out-of-order execution...

    4.  "Two tasks (only) execute on the same processor. Both tasks have memory
        requirements (data and code) that _[each?]_ exactly fits into local cache. What are the benefits
        and drawbacks of: (1) both tasks sharing the full cache, or (2) each task having exactly
        half the cache statically assigned to them?"

        1: potential for minimum cache misses if tasks do not interfere
        (i.e. if "they rarely execute at the same time),
        "each task can make full use of the cache";
        "worst-case will need to model the number of
        times the cache is, in effect, switched between the tasks".

        2: no interference, but cache misses "to be expected"...

## 2013 exam
