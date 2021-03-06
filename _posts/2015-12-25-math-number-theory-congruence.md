---
layout: post
title:  "学习同余式"
date:   2015-12-25 15:53
categories: mathematics
---

文章主要是读《什么是数学》一书中关于同余式讲解后的总结，并对一些性质和结论，自己进行了证明作为练习. 如不加说明，文中的数是指自然数.

### 1. 基本概念
同余，顾名思义，就是相同的余数，比如$$7$$和$$12$$除以$$5$$的余数都是$$2$$. 一般的，如果$$a$$和$$b$$除以$$d$$后的余数相同，就称$$a$$和$$b$$是模$$d$$同余的，用下面的记法表示：

$$a \equiv b \pmod d$$

这个记法是高斯首先使用的，当上下文含义明确时，可以省略$$\pmod d$$. 对于同余，下面三个说法是等价的：


> - $$a$$和$$b$$模$$d$$同余;
> - $$a=b+nd$$ ($$n$$为整数);
> - $$(a-b)$$被$$d$$整除.


由性质1可以推出另外两个性质. 假设$$a$$和$$b$$模$$d$$同余，且余数为$$r$$，那么


$$a=k_1d+r$$

$$b=k_2d+r$$


其中，$$k_1$$和$$k_2$$均是整数. 将两式相减得$$a-b=(k_1-k_2)d$$，即$$a=b+(k_1-k_2)d$$，令$$n=(k_1-k_2)$$即得到上面性质2. 性质3是性质2的直接推论.

同余式具有一些与普通等式类似的性质。对于等式$$a=b$$，我们有：


> - $$a$$恒等于$$a$$;
> - 若$$a=b$$，则$$b=a$$;
> - 若$$a=b$$且$$b=c$$，则$$a=c$$.


对应到同余式，则有：


> - $$a \equiv a \pmod d$$;
> - 若$$a \equiv b \pmod d$$，则$$b \equiv a \pmod d$$;
> - 若$$a \equiv b \pmod d$$且$$b \equiv c \pmod d$$，则$$a \equiv c \pmod d$$.


若再加上$$a=a'$$，$$b=b'$$，对于普通等式有：


> - $$a+b=a'+b'$$ ;
> - $$a-b=a'-b'$$ ;
> - $$ab=a'b'$$ .


相应的，若$$a \equiv a' \pmod d$$，$$b \equiv b' \pmod d$$，则对于同余式有：


> - $$a+b \equiv a'+b' \pmod d$$ ;
> - $$a-b \equiv a'-b' \pmod d$$ ;
> - $$ab \equiv a'b' \pmod d$$ .


上面三个式子的证明比较简单，以第一个为例，由于$$a$$和$$a'$$，$$b$$和$$b'$$分别模$$d$$同余，那么


$$a = a' + rd$$

$$b = b' + sd$$


其中$$r$$和$$s$$均为整数. 将两式相加得$$a+b = (a'+b') + (r+s)d$$，即$$(a+b) - (a'+b') = (r+s)d$$，这正好满足前面所述的性质2，因此$$(a+b)$$和$$(a'+b')$$模$$d$$同余。另外两个式子可以类似的证明.

### 2. 有趣的结论
利用式子$$ab \equiv a'b' \pmod d$$，可以得到一些比较有趣的结论. 我们先用$$10$$的$$n$$次幂依次除以*素数*$$11$$，并求余数. 首先，

$$10 \equiv -1 \pmod {11} $$

然后依次将该式乘以自身得到：


$$10^2 \equiv (-1)(-1) \pmod {11}$$

$$10^3 \equiv -1 \pmod {11}$$

$$\cdots$$


可以看出，$$10, 10^2, 10^3, \cdots$$依次与$$-1, 1, -1, \cdots$$模$$11$$同余. 对于任意一个十进制自然数

$$z = a_0 + a_1 \cdot 10 + a_2 \cdot 10^2 + \cdots$$

以及$$z$$的数码交替加减和

$$s = a_0 - a_1 + a_2 - a_3 + \cdots$$

我们有$$z \equiv s (mod 11)$$，即$$z$$和$$s$$除以$$11$$所得的余数相同. 因为

$$z - s = a_1 \cdot (10+1) + a_2 \cdot (10^2-1) + a_3 \cdot (10^3+1) + \cdots$$

而$$(10+1), (10^2-1), (10^3+1), \cdots$$都能被$$11$$整除，意味着$$z-s$$也能被$$11$$整除，即

$$z-s \equiv 0 \pmod {11}$$

换句话说，如果我们要求$$z$$除以$$11$$的余数，只需求$$s$$除以$$11$$的余数即可。由此可知，

> 一个数要能被$$11$$整除（即余数为$$0$$），则只需要其数码和$$s$$能被$$11$$整除即可

比如$$4-1+8-1+6-3+9 = 22$$，故$$9361814$$能被$$11$$整除.

再举个例子，如果要对$$3$$或$$9$$寻找类似的结论，我们有$$10 \equiv 1 \pmod 3$$或$$\pmod 9$$，这对于$$10^n$$都成立，故十进制数$$z=a_0 + a_1 \cdot 10 + a_2 \cdot 10^2 + \cdots$$与其数码和$$s=a_1+a_2+a_3+\cdots$$模$$3$$或$$9$$同余，因此只需要求$$s$$除以$$3$$或$$9$$的余数，就可以相应的得到$$z$$除以$$3$$或$$9$$的余数，也即，只要$$s$$能被$$3$$或$$9$$整除，那么$$z$$就能被$$3$$或$$9$$整除.

> - 练习：为数字17寻找上面类似的规律.


### 参考
[1] R. 柯朗, H. 罗宾. 什么是数学(第3版)[M]. 左平等译. 上海：复旦大学出版社, 2015.




