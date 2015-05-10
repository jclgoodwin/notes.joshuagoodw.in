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



## 6. Programming common real-time abstractions

(We assume, for now, that schedulability analysis is good and shows that deadlines can be met.)




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


