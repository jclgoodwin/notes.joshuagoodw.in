---
layout: page
title: ICOT (Spring)
---

This half of the module is the final half of the module.

* [Module website](http://www-module.cs.york.ac.uk/icot/)


## Lecture 1 -- Prefix codes

### Data compressors

Data compressor
: A symbol code $$C$$ (like a sort of function) which maps the letters $$x$$ of a random variable $$X$$ to binary strings $$C(x) \in \{0,1\}^+$$

The minimal expected length of the binary string output is bounded by the variable's Shannon entropy.

Block-encoding data compressor
: Encodes messages $$x^n \in X^n$$ as binary strings $$C(x^n)$$ (rather than doing it letter-by-letter)

Lossy compressor
: (Not of much interest to us.) Discards some information, so is associated with some probability of "failure" (of some given information being discarded). In general, we use these only when some data loss is "tolerable", and we try to tend towards discarding the "least likely messages"

Lossless compressor
: Encodes all the input messages into binary strings. If codewords are variable-length (shortening some messages and lengthening others) then of course it is more optimal to likeliness of message proportionate to shortness of binary representation

### Instantaneous decoding

What makes (the output of) some code "easy" or not to decode?

One way is if we can **identify the end of a codeword as soon as it arrives**.
A **prefix code** always allows this, which we call **instantaneous decoding**.

Prefix code
: A code where no codeword is a prefix of another

Obviously, a codeword $$c$$ is a prefix of another $$d$$ if a binary string $$t$$ exists such that $$ct = d$$.

There are visual ways of thinking of prefix codes:

1.  Using binary trees:

                 0                   1
              /    \             /      \
           00        01        10        11
          /  \      /  \      /  \      /  \
        000  001  010  011  100  101  110  111

    A code is a prefix code if no chosen codewords are descendants of each other. So this is a prefix code (codewords in `[]`):

                [0]                  1
              /    \             /      \
           00        01       [10]       11
          /  \      /  \      /  \      /  \
        000  001  010  011  100  101 [110][111]

    This is not a prefix code, although it is still uniquely decodeble:

                [0]                  1
              /    \             /      \
           00       [01]       10        11
          /  \      /  \      /  \      /  \
        000  001  010 [011] 100  101  110  111

2.  There is an unhelpful "supermarket" analogy.


## Lecture 2 -- Kraft inequality

The disparity in earnings between various people involved in making industrial cheese? Ha ha ha, no.

"For a uniquely decodable code, the codelengths ... must satisfy ..."

$$
\sum^d_{k=1} 2^{-l_k} \leq 1
$$

The holdingness of the inequality is a necessary test of the existence of a prefix code, and a sufficient test of a uniquely decodable code, for the set of codelengths.

### Completeness

A complete code is where the inequality holds with **equality**. In other words:

$$
\sum^d_{k=1} 2^{-l_k} = 1
$$

The supermarket thing I derided allows a visual interpretation of the notion of completeness.
This code is not complete (chosen codewords shown in yellow): 

<table>
  <tr>
  <td rowspan="4" bgcolor="yellow">0</td><td rowspan="2">00</td> <td>000</td>
  </tr>
  <tr>
                                                <td>001</td>
  </tr>
  <tr>
                        <td rowspan="2">01</td> <td>010</td>
  </tr>
  <tr>
                                                <td>011</td>
  </tr>
  <tr>
  <td rowspan="4">1</td><td rowspan="2">10</td> <td bgcolor="yellow">100</td>
  </tr>
  <tr>
                                                <td bgcolor="yellow">101</td>
  </tr>
  <tr>
                        <td rowspan="2">11</td> <td bgcolor="yellow">110</td>
  </tr>
  <tr>
                                                <td>111</td>
  </tr>
</table>

$$
\sum_{k=1}^{d} 2^{-l_k}
= 0.75
\neq 1
$$

By contrast, this is complete:

<table>
  <tr>
  <td rowspan="4" bgcolor="yellow">0</td><td rowspan="2">00</td> <td>000</td>
  </tr>
  <tr>
                                                <td>001</td>
  </tr>
  <tr>
                        <td rowspan="2">01</td> <td>010</td>
  </tr>
  <tr>
                                                <td>011</td>
  </tr>
  <tr>
  <td rowspan="4">1</td><td rowspan="2" bgcolor="yellow">10</td> <td>100</td>
  </tr>
  <tr>
                                                <td>101</td>
  </tr>
  <tr>
                        <td rowspan="2">11</td> <td bgcolor="yellow">110</td>
  </tr>
  <tr>
                                                <td bgcolor="yellow">111</td>
  </tr>
</table>


## Lecture 3 -- Shannon codes

(and the source coding theorem)

### Shannon codes

A Shannon code is a prefix code that (attempts to) compresses down to the Shannon entropy,
which means making codelengths equal to Shannon information contents.

Many sources are not easy to compress (their information contentses are not integers),
preventing us from saturating the Kraft inequality (in other words, meaning we can't have a "complete" code).
Nevertheless, we can make a good stab at it.

The proper length of a codeword is calculated like this:

$$
l_k = \lceil n_k \rceil = \left \lceil \log_2  \frac{1}{p_k} \right \rceil
$$

We can then buy some codewords the supermarket, making sure each is the correct length:

<table>
<tr>
<td rowspan="4">0</td><td rowspan="2">00</td> <td>000</td>
</tr>
<tr>
                                              <td>001</td>
</tr>
<tr>
                      <td rowspan="2">01</td> <td>010</td>
</tr>
<tr>
                                              <td>011</td>
</tr>
<tr>
<td rowspan="4">1</td><td rowspan="2">10</td> <td>100</td>
</tr>
<tr>
                                              <td>101</td>
</tr>
<tr>
                      <td rowspan="2">11</td> <td>110</td>
</tr>
<tr>
                                              <td>111</td>
</tr>
</table>

Like a sort of sudoku puzzle, you can't choose multiple codewords that occupy the same y coordinates (because that wouldn't give you a prefix code).

One suddenly sees the supermarket analogy in a more flattering light when one comes to start computing Shannon codes (one way of generating prefix codes). Perhaps it resembles <cite>Dale Wind-Sock's Super-Market Sweep</cite> too.

### Measuring success ('compression performance')

The "average length (bits per symbol)" is a sort of weighted average, weighted by p(k).

The achieved compression is always within 1 bit of the Shannon entropy. Only in ideal cases is it equal to the Shannon entropy.

### The source coding theorem (optimal compression)

There is an optimal prefix code $$C_{opt}$$ "with an expected length such that"...

$$
H(X) \leq L(C_{opt}, X) \leq H(X) + 1
$$

Shannon codes are never better than optimal codes (that's a tautology).
In most cases they little bit worse; in rare cases, the Shannon code is optimal.

### The incessant extra 1-bit overhead

The length of an optimal prefix code is within 1 bit of the compression limit.

"Within one bit" because information contents is not always an integer.

**Block encoding** will allow us to spread out the overhead across many letters, getting closer to the compression limit...


## Lecture 4 -- Fano codes and Huffman codes

Here we see two gradually more optimal prefix codes.

### Fano

A "greedy" algorithm (which, remember, here means something other than avaricious or gluttonous, 

### Huffman

The optimal prefix code.




## Lecture 5 -- Universal data compression

Symbol codes can only get us so far.


## Lecture 6


## Lecture 7 -- Introduction to error correction

### Parity

A parity bit ... modular addition

## Lecture 8 -- Repetition codes 

## Lecture 12 -- Linear codes: Graph representation and general bounds

(My mate General Bounds from the army, eh?)


