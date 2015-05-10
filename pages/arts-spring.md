---
layout: page
title: ARTS (Spring)
fulltitle: Analysable Real-Time Systems
---

* [Module website](http://www-module.cs.york.ac.uk/arts/ARTS_Part_1.html)


## Lecture 1

Mostly just remembering things from the previous term.

One speck of interest: one feature/property of a real-time system that I probably wouldn't think of immediately is **support for arithmetic**.


## 2. Tasks in Ada

Ada's representation of 

You can define a task type, or an individual task.


### Dynamic task creation

Can be achieved either of two ways:

1. An array of tasks, whose bounds are a non-static value.
2. Using the `new` operator on an type of `access` (pointer) to a task type.

### Task entries

### Ravenscar restrictions

* No task hierachies. All tasks at libary level.
  - The parent--child and master--dependent synchronisation would make timing analysis too difficult.
* No dynamic task creation.
* No task entries (the famous _rendezvous_ is thus impossible).

(Note that Ravenscar knowledge doesn't seem to be needed for the exam, so I won't mention it again.)

## 3. Protected objects in Ada

Protected objects are a bit like monitors (as in Java):

* Encapsulate data and operations
* Can only access data via operations, which are by definition defined to give mutual exclusion when up
* Guards can be placed on 
* "Protected actions" occur under **mutual exclusion**, guaranteeing consistent data.
* Three protected operations available:
    - Procedure
        - Can read and write update data (so concurrent calls are executed in sequence)
    - Function
        - Can only read the encapsulated data (so concurrent calls may be executed simultaneously, if the implementation decides)
    - Entry
        - Can be guarded by barriers, allowing (avoidance) **condition synchronisation**
            - A boolean expression. The entry behaves like a procedure if it's trues; otherwise, the caller goes on a queue
        - Can be private (only usable using `requeue`...)

"Procedure and function calls are mutually exclusive" (slightly ambiguous wording, but I suppose this mutual exclusion ensures consistency)

Unlike, say, tasks, these operations must be declared beforehand, not on-the-fly.

Protected objects can have priorities:

    protected type Shared_Data_Item(Initial : Data_Item; Ceiling : Priority) is
        pragma Priority(Ceiling); -- set the priority to the passed in priority (the caller's ceiling?)
        --- [...]

Ada does Immediate Priority Ceiling Inheritance. Why?

* Simpler implementation than PI or original PCP (don't have to keep track of who's blocking who)
* Caller gets raised to the ceiling
* See Autumn term

### Guards/barriers

Entries can be guarded by barriers, which are boolean expressions.
When an entry's barrier evaluates to `False`, the calling task is suspended until the barrier evaluates to `True` and there are no other tasks currently active in the protected unit.

This is how Ada supports avoidance synchronisation (a form of condition synchronisation).

Unlike with standard monitors' condition synchronisation, parameters passed by calling tasks are not visible in the barrier.
(As we will see soon, the `requeue` facility allows a work-around...)

#### Barrier evaluation

A protected object's barriers are evaluated ... when?

* When protected entry is called
* After a task executes and leaves a protected procedure or entry
    - Not a function (they are read-only)

Remember that an "open" barrier is a `True` one.

### The `select` statement

Has 100s of uses.

Wait for 1 second, and give up if the entry call takes longer:

    select 
        -- entry call
    or
        delay 1.0;
        -- do something else
    end select;

Give up if the entry call isn't immediately let through the barrier:

    select
        -- entry call;
    else
        -- do something else
    end select;


#### Conditional select

#### Select then abort

(See 12.)

### The `Count` attribute

`Entry_Name'Count` is the number of queued tasks. It is often useful in a barrier (although I don't think that's allowed in Ravenscar, not that it matters).


## 4. Resource control and the `requeue` statement

Clearly, resource control often involves allocating different quantities of resources.
This quantity might be passed as a parameter. So it's annoying that barriers don't have access to parameters.

(**Bloom's criteria** describes this and other various information that might be useful to know, making the problem even clearer.)

How to solve this? A "double interaction"? That would require atomicity to be done properly, or there'd be an opportunity for unwanted abortion.

### Requeue

    requeue Entry_Name [with abort];

### Usage example

    entry Entry_Name(paramater : something) when Some_Barrier is
    begin
        if Something
            -- something
        else
            requeue Entry_Name; -- may be a different entry name; may be in a different protected unit

By combining this with conditional things and so on in the entry body, we can express the same things as standard monitors allow, in spite of the apparent limitations of Ada's avoidance synchronisation.

Including `with abort` says "reinstate any timeouts and allow the task to be aborted" when the task is requeued. The default, "without abort", prevents this (as if it is not queued).


## 5. Clocks & time

Recall the concept of time, something humans have developed throughout history.

Programming language users need to be able to access clocks to measure the passage of time, etc.

### `Ada.Calendar`

* Like the clock on the wall. Based on implementation-dependent things.
* Notion of durations, as well as specific points in the calendar.
* Addition, subtraction, less than and greater than all included
    - operator overloading
    - infix notation
* Makes good use of Ada's type system.

Probably not suitable for measuring execution times, etc -- there is a danger of context switches, especially with lower-priority tasks.
Also, time can go fall and spring (leap seconds, etc).


### `Ada.Real_Time`

* Monotonic - only ticks forwards
* Completely divorced from `Ada.Calendar`

### Delaying a task

Relative delays:

    delay 10.0;
    -- continue executing...

The only thing this guarantees is that the task won't continue for 10.0 units of time: "accurate only in its lower bound".

On the other bound, my clock might not be granular enough (e.g. it might have a granularity of 3.0 units, delaying me for 2.0 more units resulting in an overall delay of 12.0)

Absolute delays:

    delay 


### Drift

A problem.

#### Local drift

The problem of 


#### Cumulative drift

Attempting to perform an action on average every _n_ seconds? The local drift could superimpose when absolute delays are used.

So periodic activities must be constructed carefully, in order to prevent cumulative drift:

        Interval : constant Duration := 7.0;
        Next Time : Time; -- increases by `Duration` exactly on every loop iteration
    begin
        Next_Time := Clock + Interval;
        loop
            -- action goes here
            delay until Next_Time; -- (Next_Time could be in the past, in which case no delay)
            Next_Time := Next_Time + Interval;
        end loop;
    end T;

### Timeouts

#### ...on entry calls

    select
        -- entry call goes here
    or
        delay 4.3;
        null; -- can be omitted or replaced by other statements to be executed
    end select;

#### ...on actions

    select
        delay 3.45;
    then abort
        -- action goes here
    end select;

#### Imprecise computations

A key use-case for timeouts... opportunity to compute something more precisely if there's time.

### Timing events

Allow specification of a "handler" (a bit of code -- doesn't have to be encased in a task) to be executed at a particular time.

    Set_Handler(Event : in out Timing_Event; At_Time : Time;      Handler : Timing_Event_Handler);
    Set_Handler(Event : in out Timing_Event; In_Time : Time_Span; Handler : Timing_Event_Handler);

`Handler` is a pointer to a procedure that is declared in a protected object.

See [the next lecture](#interrupt-triggered) for more on interrupt handling, which is very similar to this.


## 6. Programming common real-time abstractions

Recall things from the Autumn...

(We assume, for now, that schedulability analysis is good and shows that deadlines can be met.)

### Periodics

Basically the same as the cumulative drift solution from the previous 

### Aperiodics & sporadics

Recall that sporadics have a minimum inter-arrival time (plain old "aperiodic" non-periodics do not).

#### Interrupt-triggered

Interrupt handlers are protected procedures.

Later lectures will explain this further.

Handling interrupts:

One approach is to loop, waiting for the next interrupt each time. But interrupts are lost if not handled fast enough.

It's useful to turn off interrupts between an interrupt occurring and the next minimum inter-arrival time occurring...

### Jitter

Implementations of periodic tasks liker [what we did in the previous lecture](#cumulative-drift) are not perferct.
Competition with other tasks causes variation in when the task "completes" each period (this is called jitter).

The solution involves timing events.


## 7. Programming priority-based systems

Recall things, including that priority inheritance solves the problem of priority inversion. Bigger range of priorities allows better schedulability.

We will use the "higher/bigger number, higher/bigger/more important priority" convention (a la Linux).

### A task's base priority

    task Task_Name is
        pragma Priority(10);
        -- [...]

### Priority ceiling locking

Protected objects assigned ceilings.


# 8. Programming with other dispatching/scheduling systems


## 12. Asynchronous transfer of control

"when one task's flow of execution is changed by the action of an external agent ... [such as] another task or the passage of time"

This is a clever fragment that sort of gives `Expensive_Computation` a deadline of 1.0 seconds:

    select
        delay 1.0;
        Put_Line("Too slow");
    then abort;
        Expensive_Computation;
    end select;

It is the asynchronous select statement.

This approach to timing fault detection is simple,
and certainly notifies the errant task of the problem,
but the downside is that it enforces a particular recovery strategy (it stops the errant task) rather than possibly allowing the error to be _cured_ (perhaps by re-prioritising some priorities). A more complex 


## Timing faults and execution-time clocks


