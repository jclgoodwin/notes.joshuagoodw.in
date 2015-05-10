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

Protected objects are a bit like monitors (as in Java).

### Guards/barriers

Entries can be guarded by barriers, which are boolean expressions.
When an entry's barrier evaluates to `False`, the calling task is suspended until the barrier evaluates to `True` and there are no other tasks currently active in the protected unit.

This is how Ada supports avoidance synchronisation (a form of condition synchronisation).

Unlike with standard monitors' condition synchronisation, parameters passed by calling tasks are not visible in the barrier.
(As we will see soon, the `requeue` facility allows a work-around...)

### The `select` statement

#### Selective waits

    select 
        -- something
    or
        delay 1.0;
        --
    end select;

#### Conditional select

#### Select then abort

(See 12.)

### The `Count` attribute

`Entry_Name'Count` is the number of queued tasks. It is often useful in a barrier (although I don't think that's allowed in Ravenscar, not that it matters).


## 4. Resource control and the `requeue` statement

### Syntax

    requeue Entry_Name [with abort];

### Usage example

    entry Entry_Name(paramater : something) when Some_Barrier is
    begin
        if Something
            -- something
        else
            requeue Entry_Name; -- may be a different entry name; may be in a different protected unit

By combining this with conditional things and so on in the entry body, we can express the same things as standard monitors allow, in spite of the apparent limitations of Ada's avoidance synchronisation.



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


