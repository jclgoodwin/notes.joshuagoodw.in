---
layout: page
title: ICOT (Autumn)
---

This half of the module is half of the module.

* [Module website](http://www-module.cs.york.ac.uk/icot/)


## Lecture 1 -- Introduction

**Information** is...

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
\begin{split}
  H(X, Y)
  & = -\sum_{x, y} p(x, y) \log_2 p(x, y)
  \\
  & = \sum_{x, y} p(x, y) \log_2 \frac{ 1 }{ p(x, y) }
\end{split}
$$

"The sum is over the corresponding alphabets $$x \in X$$ and $$y \in Y$$."

#### Subaddititvity

$$
H(X, Y) \leq H(X) + H(Y)
$$

In general this can extend to sets bigger than pairs ... you can imagine.

(And if $$H(X, Y) = H(X) + H(Y)$$, the variables are independent?)


### Conditional entropy

The entropy of Y given X:

$$
H(Y|X) = \sum_x p(x)H(Y|X = x)
$$

...where $$H(Y \vert X=x) = -\sum_y p(y \vert x) log_2 p(y \vert x)$$.

An alternative definition:

$${H(Y|X) = -\sum_{x, y} p(x, y) log_2 p(y, x)}$$


### Marginal entropy

Recall the marginal distribution:

$$
p(x) = \sum_y p(x, y)
$$


### Chain rule

...connecting joint and conditional entropies:

$$
\begin{split}
  H(X, Y)
  & =
  H(Y|X) + H(X) 
  \\
  & =
  H(X|Y) + H(Y)
\end{split}
$$




## Relative entropy and mutual information 

(Lecture 5)

"Two related notions"


### Relative entropy

The relative entropy between "two probability distributions $$p(x)$$ and $$q(x)$$ on the same alphabet $$X$$":

$$
D\left(p||q\right) = \sum_{x}p(x)\log_{2}\frac{p(x)}{q(x)}
$$

"where we adopt the convention that
$$0\log\frac{0}{q} = 0\log\frac{0}{0} = 0$$
and
$$p\log\frac{p}{0} = \infty$$
(by continuity)"

Relative entropy is not symmetric.


### Mutual information

"The relative entropy between the joint distribution and the joint distribution and the product distribution $$p(x)p(y)$$."

$$
I(X:Y)=D[p(x,y)||p(x)p(y)]=\sum_{x,y}p(x,y)\log_{2}\frac{p(x,y)}{p(x)p(y)}
$$

Mutual information _is_ symmetric (it's mutual)

Alternative definition: difference between two Shannon entropies:

$$
\begin{split}
I(X:Y)
& =
H(X)-H(X|Y) 
\\
& =
H(Y)-H(Y|X) 
\end{split}
$$

What mutual information is is the sort of _intersection_ between the information contentses of $$X$$ and $$Y$$.

In a communication channel, mutual information, "which corresponds to a difference
of entropies, quantifies the information which is transmitted by Alice and
acquired by Bob". In a noiseless channel, this = the input variable's full information
content.

#### Conditional mutual information

"given a third variable $$Z$$" (or more):

$$
I(X : Y | Z) = H(X|Z) - H(X | Y, Z)
$$

"a simple consequence of the chain rule for entropy"




## Gaussian channels

(Lecture 11)

(Notes largely on paper...)

### Capacity

$$
C = \frac{1}{2} log_2 \left( 1 + \frac{ S }{ N } \right)
$$

C
: capacity (bits per second)

S
: signal (or power)

N
: noise


### Capacity of a bandlimited Gaussian channel

$$
C = W log_2 \left( 1 + \frac{ S }{ N } \right)
$$

W
: bandwidth (Hz)


### Parallel Gaussian channels


$$
C = C_1 + \ldots + C_n
$$

#### 'Water-filling'

(See example below)




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
\begin{split}
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
\end{split}
$$




### 4. Water-filling

(See lecture 11)

"Suppose that you want to use these channels in
parallel to communicate data and you are limited to a
total input power of $$P = 6$$"

"How do you distribute the power among the channels
in order to maximize the transmission rate?"

 Channel|Noise|Optimal power|Noise + power
|-----------------------------------------|
 1      |1    |3.5          |4.5
 2      |3    |1.5          |4.5
 3      |4    |0.5          |4.5
 4      |4    |0.5          |4.5

("Pour" "water" until all have as equal noise + power as possible.)


## 2013 exam

1.  [20 marks]

    1.

2.  

3.  [50 marks] [!]

4.  [15 marks]

    1.  (8 marks) Water-filling, easy

    2.  (7 marks) Capacity of the global channel (made up of parallel Gaussian channels):

        $$
          \begin{aligned}
                  C & = C_1 + C_2 + C_3 + C_4
          \\
                C_k & = \frac{1}{2} log_2 \left( 1 + \frac{ P_k }{ N_k } \right)
          \\
          C_1 = C_2 & = \frac{1}{2} log_2 \left( 1 + \frac{ 3 }{ 1 } \right) = 1
          \\
                C_3 & = \frac{1}{2} log_2 \left( 1 + \frac{ 1 }{ 3 } \right) = 0.2075
          \\
                C_4 & = 0
          \\
                  C & = 1 + 1 + 0.2075 + 0 = 2.2075
          \end{aligned}
        $$

5.  [15 marks] "Consider a telephone line which can be approximated
    by a bandlimited Gaussian channel with bandwidth W =
    10 kHz.
    What is the minimum signal-to-noise ratio (expressed
    in dB) which enables two parties to
    communicate at the rate of 100 kbits per second?"
    
    $$
    \begin{aligned}
      & C = W log_2 \left( 1 + \frac{S}{N} \right)
      \\
      & 100000 = 10000 log_2 \left( 1 + \frac{S}{N} \right)
      \\
      & \therefore log_2 \left( 1 + \frac{S}{N} \right) = 10
      \\
      & \therefore \frac{S}{N} + 1 = 2^10
      \\
      & 10 log_{10} 1023 = 30.1 dB
    \end{aligned}
    $$
