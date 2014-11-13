---
layout: page
title: ICOT (Autumn)
---

This half of the module is half of the module.

* [Module website](http://www-module.cs.york.ac.uk/icot/)


## Lecture 1 -- Introduction

Information is...

* a "decrease of ignorance about a source"
* a "decrease of entropy for a variable"

Entropy and information are measured in bits.

Entropy quantifies the number of different values a variable can take. One bit of entropy = two values because $\log 2 = 1$.

I could go into more detail but it all makes sense.


## Lecture 2 -- Probability

Recall Bayes's theorem 



## Joint and conditional entropy

### Joint entropy

Given a pair of random variables {}

$$
H(X,Y)=-\sum_{x,y} p(x,y) \log_2(x,y)
$$



## Summarizing practical

### 1. Probability and Shannon entropy

Two random variables: $$X=\{x, p(x)\}$$ and $$Y=\{y, p(y)\}$$

Ternary alphabet: $$\{0, 1, 2\}$$

|       |Y = 0              |Y = 1              |Y = 2|
|X = 0  |$$\frac{1}{4}$$    |$$\frac{1}{4}$$    |0|
|X = 1  |$$\frac{1}{16}$$   |$$\frac{1}{16}$$   |$$\frac{1}{8}$$|
|X = 2  |$$\frac{1}{12}$$   |$$\frac{1}{12}$$   |$$\frac{1}{12}$$|

"First, verify that the table specifies a valid probability distribution:"

$$
\tfrac{1}{4}  + \tfrac{1}{4}  + 0 +
\tfrac{1}{16} + \tfrac{1}{16} + \tfrac{1}{8} +
\tfrac{1}{12} + \tfrac{1}{12} + \tfrac{1}{12} =
1
$$

1.  "Compute the joint entropy $$H(X,Y)$$"

$$
\begin{aligned}
-(
&
\tfrac{1}{4} \log_2 \tfrac{1}{4}
+
\tfrac{1}{4} \log_2 \tfrac{1}{4}  
+

0\\ &
+
\tfrac{1}{16} \log_2 \tfrac{1}{16} 
+
\tfrac{1}{16} \log_2 \tfrac{1}{16} 
+
\tfrac{1}{8} \log_2 \tfrac{1}{8} 
\\ &
+
\tfrac{1}{12} \log_2 \tfrac{1}{12} 
+
\tfrac{1}{12} \log_2 \tfrac{1}{12} 
+
\tfrac{1}{12} \log_2 \tfrac{1}{12} 
)
\end{aligned}
$$




### 4. Water-filling

(See lecture 11)

"Suppose that you want to use these channels in
parallel to communicate data and you are limited to a
total input power of $$P=6$$"

"How do you distribute the power among the channels
in order to maximize the transmission rate?"

 Channel|Noise|Optimal power|Noise + power
|-----------------------------------------|
 1      |1    |3.5          |4.5
 2      |3    |1.5          |4.5
 3      |4    |0.5          |4.5
 4      |4    |0.5          |4.5

(Pour water until all have equal noise + power, )

Goa

