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
