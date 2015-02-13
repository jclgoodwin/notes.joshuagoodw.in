---
layout: page
title: ARTS (Spring)
fulltitle: Analysable Real-Time Systems
---

* [Module website](http://www-module.cs.york.ac.uk/arts/ARTS_Part_1.html)


## Lecture 1

Mostly just remembering things from the previous term.

One speck of interest: one feature/property of a real-time system that I probably wouldn't think of immediately is support for aritmetic.


## 12. Asynchronous transfer of control

"when one task's flow of execution is changed by the action of an external agent ... [such as] another task or the passage of time"

This is a clever fragment that sort of gives `Expensive_Computation` a deadline of 1.0 seconds:

	select
		delay 1.0;
		Put_Line("Too slow");
	then abort;
		Expensive_Computation;
	end select;


## clocks
