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


## Summer 2013 exam paper

1.  1.  Shannon:

        | $$x$$ | $$p(x)$$ | Information contents ($$\left \lceil \log_2  \frac{1}{p_k} \right \rceil$$) | Suitable codeword
        |-------|----------|---|----
        |   a   |   0.40   | 2 | 00
        |   b   |   0.19   | 3 | 011
        |   c   |   0.18   | 3 | 010
        |   d   |   0.13   | 3 | 101
        |   e   |   0.10   | 4 | 1110
  
        Average length:
  
              2 × 0.40
            + 3 × (0.19 + 0.18 + 0.13)
            + 4 × 0.10
            -----------------
            = 0.8 + 0.4 + 1.5
            -----------------
            = 2.7 bits per symbol

    2.  Fano:

        Code:

        <table>
          <thead>
            <tr>
              <th>x</th>
              <th>p(x)</th>
              <th>Split</th>
              <th></th>
              <th>Split</th>
              <th></th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>a</td>
              <td>0.40</td>
              <td rowspan="2">0.59</td>
              <td>0</td>
              <td></td>
              <td><strong>00</strong></td>
              <td></td>
            </tr>
            <tr>
              <td>b</td>
              <td>0.19
              
              <td>0</td>
              <td></td>
              <td><strong>01</strong></td>
              <td></td>
            </tr>
            <tr>
              <td>c</td>
              <td>0.18</td>
              <td rowspan="3">0.61</td>
              <td>1</td>
              <td>0.18</td>
              <td><strong>10</strong></td>
              <td></td>
            </tr>
            <tr>
              <td>d</td>
              <td>0.13</td>
              <td>1</td>
              <td rowspan="2">0.23</td>
              <td>11</td>
              <td><strong>110</strong></td>
            </tr>
            <tr>
              <td>e</td>
              <td>0.10</td>
              <td>1</td>
              <td>11</td>
              <td><strong>111</strong></td>
            </tr>
          </tbody>
        </table>

        Average length:

              2 × 0.40
            + 2 × 0.19
            + 2 × 0.18
            + 3 × 0.13
            + 3 × 0.10
            ----------------------------------
            = 0.80 + 0.38 + 0.36 + 0.39 + 0.30
            ----------------------------------
            = 2.23 bits per symbol

    4.  Huffman:

        | $$x$$ | $$p(x)$$ | Suitable codeword
        |-------|----------|------------------
        |   a   |   0.40   | 
        |   b   |   0.19   | 
        |   c   |   0.18   |   
        |   d   |   0.13   |    1
        |   e   |   0.10   |    0

        <table>
          <thead><th>x</th><th>p(x)</th><th>Suitable codeword</th></tr></thead>
          <tbody>
            <tr>
              <td>a</td>
              <td>0.40</td>
              <td></td>
            </tr>
            <tr>
              <td>b</td>
              <td>0.19</td>
              <td><span style="background:yellow">1</span></td>
            </tr>
            <tr>
              <td>c</td>
              <td>0.18</td>
              <td><span style="background:yellow">0</span></td>
            </tr>
            <tr>
              <td>d</td>
              <td rowspan="2">0.23</td>
              <td>1</td>
            </tr>
            <tr>
              <td>e</td>

              <td>0</td>
            </tr>
          </tbody>
        </table>

        <table>
          <thead><th>x</th><th>p(x)</th><th>Suitable codeword</th></tr></thead>
          <tbody>
            <tr>
              <td>a</td>
              <td>0.40</td>
              <td></td>
            </tr>
            <tr>
              <td>b</td>
              <td rowspan="2">0.37</td>
              <td><span style="background:yellow">1</span>1</td>
            </tr>
            <tr>
              <td>c</td>

              <td><span style="background:yellow">1</span>0</td>
            </tr>
            <tr>
              <td>d</td>
              <td rowspan="2">0.23</td>
              <td><span style="background:yellow">0</span>1</td>
            </tr>
            <tr>
              <td>e</td>

              <td><span style="background:yellow">0</span>0</td>
            </tr>
          </tbody>
        </table>

        <table>
          <thead><th>x</th><th>p(x)</th><th>Suitable codeword</th></tr></thead>
          <tbody>
            <tr>
              <td>a</td>
              <td>0.40</td>
              <td><span style="background:yellow">0</span></td>
            </tr>
            <tr>
              <td>b</td>
              <td rowspan="4">0.60</td>
              <td><span style="background:yellow">1</span>11</td>
            </tr>
            <tr>
              <td>c</td>

              <td><span style="background:yellow">1</span>10</td>
            </tr>
            <tr>
              <td>d</td>

              <td><span style="background:yellow">1</span>01</td>
            </tr>
            <tr>
              <td>e</td>

              <td><span style="background:yellow">1</span>00</td>
            </tr>
          </tbody>
        </table>

        Average length:

              0.40 × 1
            + 0.60 × 3
            ----------
            = 2.20 bits per symbol

2.  1.  Counting:

             rowrowyourboatgentlydownthestream | Frequency
             r  r     r                   r    | 4
              o  o  o   o         o            | 5
               w  w                w           | 3
                   y            y              | 2
                     u                         | 1
                       b                       | 1
                         a                  a  | 2
                          t   t      t   t     | 4
                           g                   | 1
                            e          e   e   | 3
                             n      n          | 2
                               l               | 1
                                 d             | 1
                                      h        | 1
                                        s      | 1
                                             m | 1
    2.  Huffman:

             r   w   t   n   a   o   w   y   m   s   h   d   l   g   b   u
            ---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---
             4   3   4   2   2   5   3   2  [1] [1] [1] [1] [1] [1] [1] [1]
             |   |   |   |   |   |   |   |    \ /     \ /     \ /     \ /
             4   3   4  [2] [2]  5   3   2    [2]     [2]     [2]     [2]
             |   |   |    \ /    |   |   |       \   /           \   /
             4   3   4     4     5  [3] [2]        4               4
             |   |   |     |     |    \ /          |               |
            [4] [3]  4     4     5     5           4               4 
              \ /    |     |     |     |           |               |
               7    [4]   [4]    5     5          [4]             [4]
               |       \ /       |     |               \       /
               7        8       [5]   [5]                  8
               |        |          \ /                     |
              [7]      [8]         10                      8
                 \    /             |                      |
                   15             [10]                    [8]
                                         \            /
                                               18

        Symbol|Expected codeword length|||||       |      |       |        |Codeword
        -|
        r     |3|     |      |      | **0**|      0|     0| **0**0|      00|**0**00|
        w     |3|     |      |      | **1**|      1|     1| **0**1|      01|**0**01|
        t     |3|     |      |      |      |  **0**|     0| **1**0|      10|**0**10|
        n     |4|     | **0**|   0  |     0| **1**0|    10|**1**10|     110|**0**110|
        a     |4|     | **1**|   1  |     1| **1**1|    11|**1**11|     111|**0**111|
        o     |3|     |      |      |      |       | **0**|      0|  **0**0| **1**00|
        w     |4|     |      | **0**|     0|      0|**1**0|     10| **0**10| **1**010|
        y     |4|     |      | **1**|     1|      1|**1**1|     11| **0**11| **1**011|
        m     |5|**0**|**0**0|  00  |    00|**0**00|   000|    000|**1**000|**1**1000|
        s     |5|**1**|**0**1|  01  |    01|**0**01|   001|    001|**1**001|**1**1001|
        h     |5|**0**|**1**0|  10  |    10|**0**10|   010|    010|**1**010|**1**1010|
        d     |5|**1**|**1**1|  11  |    11|**0**11|   011|    011|**1**011|**1**1011|
        l     |5|**0**|**0**0|  00  |    00|**1**00|   100|    100|**1**100|**1**1100|
        g     |5|**1**|**0**1|  01  |    01|**1**01|   101|    101|**1**101|**1**1101|
        b     |5|**0**|**1**0|  10  |    10|**1**10|   110|    110|**1**110|**1**1110|
        u     |5|**1**|**1**1|  11  |    11|**1**11|   111|    111|**1**111|**1**1111|

    3.  Average length:

        Symbol|Frequency|Length|Frequency × Length
        ------|---------|------|
        r     |    4    |   3  | 12
        w     |    3    |   3  | 9
        t     |    4    |   3  | 12
        n     |    2    |   4  | 8
        a     |    2    |   4  | 8
        o     |    5    |   3  | 15
        w     |    3    |   4  | 12
        y     |    2    |   4  | 8
        m     |    1    |   5  | 5
        s     |    1    |   5  | 5
        h     |    1    |   5  | 5
        d     |    1    |   5  | 5
        l     |    1    |   5  | 5
        g     |    1    |   5  | 5
        b     |    1    |   5  | 5
        u     |    1    |   5  | 5
        ======|=========|======|===
              |    33   |      | 124

        124 ÷ 33 ≈ 3.757 bits per symbol

    4.  Estimate bits per letter gained by the compression:

        16 letters. $$log_2 16 = 4$$. $$4 - 3.576 = 0.423$$

3.  A bipartite graph:

    ![](../images/icot-summer-2013-3-bipartite-graph.svg)

    1.  "Derive the parity-check matrix H in systematic form"

        This seems like a sensible labelling:

             p1 —— [A] —— s1 —— [B] —— p2
                    |     |      |
                   s2 —— [C] —— s3
                          |
                          p3


        |                     | s1  | s2  | s3  | p1  | p2  | p3  |
        |---------------------|-----|-----|-----|-----|-----|-----|
        | Parity-check node A |**1**|**1**|  0  |**1**|  0  |  0  |
        | Parity-check node B |**1**|  0  |**1**|  0  |**1**|  0  |
        | Parity-check node C |**1**|**1**|**1**|  0  |  0  |**1**|

        So the code is characterised by this matrix:

        $$
        H =
        \begin{pmatrix}
        1 & 1 & 0 & 1 & 0 & 0 \\
        1 & 0 & 1 & 0 & 1 & 0 \\
        1 & 1 & 1 & 0 & 0 & 1
        \end{pmatrix}
        $$

        In systematic form:

        $$
        H = \begin{pmatrix} P & I_3 \end{pmatrix}
        $$

        where

        $$
        P =
        \begin{pmatrix}
        1 & 1 & 0 \\
        1 & 0 & 1 \\
        1 & 1 & 1
        \end{pmatrix}
        $$

        (lecture 11, slide 8)

    2.  "Derive the generator matrix G"

        (lecture 11, slide 8)

        $$
        G = 
        \begin{pmatrix}
        I_3 \\
        P
        \end{pmatrix}
        =
        \begin{pmatrix}
        1 & 0 & 0 \\
        0 & 1 & 0 \\
        0 & 0 & 1 \\
        1 & 1 & 0 \\
        1 & 0 & 1 \\
        1 & 1 & 1
        \end{pmatrix}
        $$

    3. "Determine the basic parameters $$[n, k, d]$$ ..."

        Number of bits in codewords: 3

        Number of bits in input messages: 3

        Distance (number of bits in which input and output differ): 3

        So: $$[3, 3, 3]$$

    4.  "Construct all the codewords"

        $$
        \mathbf{c}
        = G\mathbf{s}
        = \begin{pmatrix} I_3 \\ P \end{pmatrix} \mathbf{s}
        = \begin{pmatrix} \mathbf{s}\\ P\mathbf{s} \end{pmatrix}
        $$

        (I don't quite understand the last step..)

        We need to compute the parity bits ($$P\mathbf{s}$$) for each possible source three-bit word, and then concatenate source bits with them:

        | s | $$P\mathbf{s}$$ | c |
        |----
        | 000 | $$\begin{pmatrix}1&1&0\\1&0&1\\1&1&1\end{pmatrix} \begin{pmatrix}0\\0\\0\end{pmatrix} = \begin{pmatrix}0 + 0 + 0\\0 + 0 + 0\\0 + 0 + 0\end{pmatrix} = 000$$ | 000000
        | 001 | $$\begin{pmatrix}1&1&0\\1&0&1\\1&1&1\end{pmatrix} \begin{pmatrix}0\\0\\1\end{pmatrix} = \begin{pmatrix}1\times0 + 1\times0 + 0\times1 \\1\times0 + 0\times0 + 1\times1 \\1\times0 + 1\times0 + 1\times1\end{pmatrix} = \begin{pmatrix}0\\1\\1\end{pmatrix} = 011$$ | 001011
        | 010 | $$\begin{pmatrix}1&1&0\\1&0&1\\1&1&1\end{pmatrix} \begin{pmatrix}0\\1\\0\end{pmatrix} = \begin{pmatrix}1\times0 + 1\times1 + 0\times0 \\1\times0 + 0\times1 + 1\times0 \\1\times0 + 1\times1 + 1\times0\end{pmatrix} = \begin{pmatrix}1\\0\\1\end{pmatrix} = 101$$ | 010101
        | 011 | 110 | 011110
        | 100 | 111 | 100111  
        | 101 | 100 | 101100 
        | 110 | 010 | 110010 
        | 111 | 001 | 111001 

4.  See the "practical recipe" (lecture 12)

    1.  $$[n, k, d] = [15, 11, 3]$$

        $$r = n - k = 4$$

        A matrix of all 15 non-zero 4-bit binary words:

        $$
        H =
        \begin{pmatrix}
        0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 1 & 1 & 1 & 1 & 1 & 1 & 1 \\
        0 & 0 & 0 & 1 & 1 & 1 & 1 & 0 & 0 & 0 & 0 & 1 & 1 & 1 & 1 \\
        0 & 1 & 1 & 0 & 0 & 1 & 1 & 0 & 0 & 1 & 1 & 0 & 0 & 1 & 1 \\
        1 & 0 & 1 & 0 & 1 & 0 & 1 & 0 & 1 & 0 & 1 & 0 & 1 & 0 & 1
        \end{pmatrix}
        $$

    2.  Permuted in systematic form:

        $$H = \begin{pmatrix}P & I_4 \end{pmatrix}$$

        $$
        \begin{pmatrix}
        0 & 0 & 0 & 0 & 1 & 1 & 1 & 1 & 1 & 1 & 1 \\
        0 & 1 & 1 & 1 & 0 & 0 & 0 & 1 & 1 & 1 & 1 \\
        1 & 0 & 1 & 1 & 0 & 1 & 1 & 0 & 0 & 1 & 1 \\
        1 & 1 & 0 & 1 & 1 & 0 & 1 & 0 & 1 & 0 & 1
        \end{pmatrix}
        $$

        $$
        I_4 = 
        \begin{pmatrix}
        1 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 \\
        0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 1
        \end{pmatrix}
        $$

    3.  Generator matrix:

        $$
        G = \begin{pmatrix} I_11 \\ P \end{pmatrix}
        =
        \begin{pmatrix}
        1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
        0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
        0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
        0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
        0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 \\
        0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 \\
        0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 \\
        0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 \\
        0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 \\
        0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 \\
        0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 \\
        0 & 0 & 0 & 0 & 1 & 1 & 1 & 1 & 1 & 1 & 1 \\
        0 & 1 & 1 & 1 & 0 & 0 & 0 & 1 & 1 & 1 & 1 \\
        1 & 0 & 1 & 1 & 0 & 1 & 1 & 0 & 0 & 1 & 1 \\
        1 & 1 & 0 & 1 & 1 & 0 & 1 & 0 & 1 & 0 & 1
        \end{pmatrix}
        $$

    4.  Encode the sequence $$00000000000$$:

        Every row in $$G$$ has an odd number of $$1$$s, so ... 00000000000 0000

        Encode the sequence $$11111111111$$:

        11111111111 1111

