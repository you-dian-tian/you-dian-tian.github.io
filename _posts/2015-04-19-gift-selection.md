---
layout: post
title:  "大神与三位小伙伴"
date:   2015-04-19 18:22
categories: Algorithms
---

该题是[微软编程之美2015测试赛的第二题][链接]，题意大概是这样的：

*有$$A$$、$$B$$和$$C$$三类纪念品，每类纪念品有$$N$$种。第$$i$$种纪念品分别记作$$A_i$$、$$B_i$$和$$C_i$$，它们的价值均为$$i$$（$$1\le i\le N$$），且分别有$$N+1-i$$个剩余。现在希望从这三类纪念品种各选一个，但不能有两个纪念品的价值相同而另一个价值不同，即要么三个纪念品的价值一样，要么各不一样。问给定$$N$$，有多少种选择方案？求出方案数模$$(10^9+7)$$的结果。范围限制：$$1 \le N \le 10^{18}$$.*

`分析`：用$$(i, j, k)$$表示选择价值为$$i$$的纪念品$$A$$、价值为$$j$$的纪念品$$B$$和价值为$$k$$的纪念品$$C$$，对应于$$(i, j, k)$$的选择方案数有多个，用$$g(i, j, k)$$表示：

$$
g(i,j,k)=(N+1-i)×(N+1-j)×(N+1-k)
$$

再设$$F(N)$$表示满足题目要求的总的方案数量（$$F$$是关于$$N$$的函数），则有：

$$
F(N)=\sum_{i=1}^N\sum_{j=1}^N\sum_{k=1}^N[i \ne j \land j \ne k \land k \ne i]g(i,j,k)
$$

记法$$[P]$$的含义是：

$$
\begin{equation}
[P] = \begin{cases}
1, & P\text{为真} \\
0, & P\text{为假}
\end{cases}
\end{equation}
$$

对上面的式子进行转化，消去$$[i \ne j \land j \ne k \land k \ne i]$$以便运算：

$$
F(N)=\sum_{i=1}^N\sum_{j=1}^N\sum_{k=1}^Ng(i,j,k) - \sum_{i=1}^N\sum_{j=1}^Ng(i,i,j) - \sum_{i=1}^N\sum_{j=1}^Ng(i,j,i) - \sum_{i=1}^N\sum_{j=1}^Ng(j,i,i) + 3\sum_{i=1}^Ng(i,i,i)
$$

上式中最后加上了三个纪念品价值相同的情况，因为在减去两个纪念品价值相同的方案数时把三个纪念品价值相同的方案数也减去了。根据$$g(i,j,k)$$的定义注意到

$$
\sum_{i=1}^N\sum_{j=1}^Ng(i,i,j) = \sum_{i=1}^N\sum_{j=1}^Ng(i,j,i) = \sum_{i=1}^N\sum_{j=1}^Ng(j,i,i)
$$

因此

$$
\begin{align}
F(N) ={} & \sum_{i=1}^N\sum_{j=1}^N\sum_{k=1}^Ng(i,j,k) - 3\sum_{i=1}^N\sum_{j=1}^Ng(i,i,j) + 3\sum_{i=1}^Ng(i,i,i) \\

     ={} & \sum_{i=1}^N\left(\sum_{j=1}^N\sum_{k=1}^Ng(i,j,k) - 3\sum_{j=1}^Ng(i,i,j) + 3g(i,i,i)\right) \\

     ={} & \sum_{i=1}^N\left(\sum_{j=1}^N\left(\sum_{k=1}^Ng(i,j,k) - 3g(i,i,j)\right) + 3g(i,i,i)\right) \\

     ={} & \sum_{i=1}^N\left(\sum_{j=1}^N\left((N+1-i)(N+1-j)\sum_{k=1}^N(N+1-k) - 3g(i,i,j)\right) + 3g(i,i,i)\right) \\

     ={} & \sum_{i=1}^N\left(\sum_{j=1}^N\left((N+1-i)(N+1-j) \cdot \frac{N(N+1)}{2} - 3g(i,i,j)\right) + 3g(i,i,i)\right)
\end{align}	 
$$

有兴趣的读者可以自行演算下去，这儿不给出后续推导过程了，最后的结果为

$$
F(N)=\frac{N^2(N+1)^2(N^2-3N+4)}{8}, N \ge 3
$$

结果中$$N \ge 3$$是因为当$$N=2$$时上式给出的结果不正确。或许本题有更好的思路，手工推导公式的过程容易出错，但好处是一旦得到关于$$N$$的公式，计算起来就很方便了，当$$N$$非常大时尤其如此。

####参考

*Concrete Mathematics, a foundation for computer science* by Graham, Knuth and Patashnik. 2th ed. 第二章


[链接]: http://hihocoder.com/contest/msbop2015warmup/problem/2