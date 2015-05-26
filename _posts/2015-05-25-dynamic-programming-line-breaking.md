---
layout: post
title:  "Dynamic Programming - Line Breaking"
date:   2015-05-25 21:53
categories: Dynamic Programming
---

# The Problem #

Suppose you have an article which contains $$n$$ words $$w_1, w_2, ..., w_n$$, where $$w_i$$ consists of $$c_i$$ characters. The line breaking problem asks you to partition the $$n$$ words into lines, with each two adjacent words in the same line separated by a space, and there is no space after the last word of each line. In addition, we have a max line length $$L$$, so if in your partition a line consists of $$w_i, w_{i+1}, ..., w_j$$, the following inequality must be satisfied:

$$\sum_{k = i}^{j-1} (c_i + 1) + c_j \leq L$$

We use $$Len(i, j)$$ to denot the left part of this inequality. The difference between the left-hand side and the right-hand side is called *slack*. Now we further assume you are using a fixed-width font and ignore punctucation prolems. Give an algorithm to partition the $$n$$ words into lines with the sum of squares of slacks of each line as small as possible.

# My Solution #

Remember that when solving a problem using dynamic programming, a good start is to define a set of subproblems of the original problem. We first observe that for some $$j \leq n$$, $$w_j, w_{j+1}, ..., w_n$$ must belong to the last line. This give us the subproblem $$w_1, w_2, ..., w_{j-1}$$. Let $$opt(j)$$ denote the minimum value we can obtain by optimally arranging the first $$j$$ words into lines, with the length of each line not greater than $$L$$. For each $$j (1 \leq j \leq n)$$, we try all the possible cases that $$w_i, w_{i+1}, ..., w_j$$ may form a line for $$1 \leq i \leq j$$. If we use $$S(i, j)$$ to denote the square of the *slack* when using words $$w_i$$ to $$w_j$$ to form a line, that is

$$S(i, j) = \left(\sum_{k = i}^{j-1} (c_i + 1) + c_j - L\right)^2 = \left(Len(i, j) - L\right)^2$$

we have the following recurrence:

$$
opt(j) = \min_{1 \leq i \leq j \land Len(i, j) \leq L}\left(S(i, j) + opt(i)\right), 1 \leq j \leq n
$$

The initial case is trivial, $$opt(1) = S(1, 1) $$. The pesudo-code would be

{% highlight c linenos %}

    opt[1] = S(1, 1)
    for j = 2, 3, ..., n
        opt[j] = INFINITE
        for i = j, j-1, ..., 1
            if Len(i, j) <= L
                opt[j] = min(opt[j], S(i, j) + opt[i])
            else
                break
    
    return opt[n]

{% endhighlight %}

A further note, since $$S(i, j)$$ is defined in terms of $$Len(i, j)$$, we could calculate $$Len(i, j)$$ for each $$i$$ and $$j$$ in advance in $$O(n^2)$$ time and use it as needed. You may wonder how could we know which word belongs to which line? That's not a problem, we can trace back the opt[i] array and calculate the line boundaries along the way using the same logic as above:

{% highlight c linenos %}
print-solution(n)
    minSquareOfSlack = INIFINITE
    for i = n, n-1, ..., 1
        if Len(i, n) <= L
            minSquareOfSlack = min(minSquareOfSlack, S(i, n) + opt[i])
        else
            break
	    
    print "word i through n form a line"
    print-solution(i-1)

{% endhighlight %}