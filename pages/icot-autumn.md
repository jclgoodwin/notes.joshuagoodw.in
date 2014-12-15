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

Entropy quantifies the number of different values a variable can take. One bit of entropy = two values because $$ \log_2 2 = 1 $$.

№ of _bits of information_ ≤ № of _bit symbols_. For example, a variable may have take the values `01010` or `11000`
(each with probability ½ -- an important condition): that's one bit of information, but more than one symbol.
(Clearly this representation of the variable is sort of inefficient, not optimal.) 

I could go into more detail but it all makes sense.




## Lecture 2 -- Probability

$$
p(a \vee b) = p(a) + p(b)
$$

$$
p(a \wedge b) = p(a) \times p(b)
$$

Recall the chain rule:

$$
p(x, y) = p(x|y)p(y) = p(y|x)p(x)
$$

Recall Bayes's theorem:

$$
p(x|y)=\frac{p(y|x)p(x)}{p(y)}
$$

### 'Random' or 'stochastic' variables

$$
X = \{ x, p(x) \}
$$

..."where the generic outcome $$x$$ occurs with probability $$P(X=x)=p(x)$$. The
outcome $$x$$ is extracted from a set of $$n$$ elements (range)"

* $$X$$ may be called a **source** or ensemble
* $$x$$ may be called a letter or symbol
* The range $$X = \{x_1, \cdots, x_n\}$$ may be called the **alphabet**




## Joint and conditional entropy


### Joint entropy

Given a pair of random variables $$(X, Y)$$:

$$
\begin{aligned}
  H(X, Y)
  &= -\sum_{x, y} p(x, y) \log_2 p(x, y)
  \\
  &= \sum_{x, y} p(x, y) \log_2 \frac{ 1 }{ p(x, y) }
\end{aligned}
$$

"The sum is over the corresponding alphabets $$x \in X$$ and $$y \in Y$$."

#### Subaddititvity

$$
H(X, Y) \leq H(X) + H(Y)
$$

In general this can extend to set bigger than pairs ... you can imagine.

(And if $$H(X, Y) = H(X) + H(Y)$$, the variables are independent?)


### Conditional entropy

$$
H(Y|X) = \sum_x p(x)H(Y|X = x)
$$

where


### Chain rule

...connecting joint and conditional entropies.

$$
\begin{aligned}
  H(X, Y)
  =&
  H(Y|X) + H(X) 
  \\
  =&
  H(X|Y) + H(Y)
\end{aligned}
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

"Compute the joint entropy $$H(X ,Y)$$"

$$
\begin{aligned}
-(
&
  \tfrac{1}{4} \log_2 \tfrac{1}{4}
+ \tfrac{1}{4} \log_2 \tfrac{1}{4}
+ 0
\\
&
+ \tfrac{1}{16} \log_2 \tfrac{1}{16}
+ \tfrac{1}{16} \log_2 \tfrac{1}{16}
+ \tfrac{1}{8}  \log_2 \tfrac{1}{8}
\\
&
+ \tfrac{1}{12} \log_2 \tfrac{1}{12}
+ \tfrac{1}{12} \log_2 \tfrac{1}{12}
+ \tfrac{1}{12} \log_2 \tfrac{1}{12}
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

("Pour" "water" until all have as equal noise + power as possible.)
