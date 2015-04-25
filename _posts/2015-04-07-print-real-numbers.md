---
layout: post
title:  "打印实数"
date:   2015-04-07 12:00
categories: Exercises
---

在不借助库函数的情况下，如何输出一个实数？

初步想法是分成三步：

- 打印整数部分；
- 打印小数点；
- 打印小数部分。

若将小数部分转化为整数来输出，那么实际上就只有两个步骤，实现打印整数是核心。作为一个练习，这儿将打印一个整数以递归的方式来实现，见下面的`pring_int`函数。由于`double`类型存储小数时精确度有限，这儿取15位数字，即整数位数加上小数部分的位数为15，经过试验，取15位最贴近实际的数值，与用`printf`打印的实数基本无差别。需要指出的是，下面实现的`print_fract`用于打印小数部分，没有考虑效率问题，仅仅是不停的对`f`乘以10，只要`f`还大于0，那么其小数数位就还存在，每次打印乘以10后多出来的整数部分。

下面的算法还简单的考虑了是否需要打印小数点的情况，即如果输入就是整数，那么没必要打印小数点和小数部分了。

{% highlight c linenos %}
/*
 * print_real_numbers.c - Print a real number without the help of library.
 *
 * BUG: can't handle negative numbers or very large numbers.
 *
 * History:
 *
 *   Created on 2015-4-10 by YU Heng-yang.
 */
#include <stdio.h>
#include <assert.h>

typedef long long ll;

#define PRECISION 15

int nint = 0;

void print_digit(int d)
{
	assert(d >= 0 && d < 10);
	putchar(d + '0');
}

void print_int(ll n)
{
	nint++;
	if (n < 10)
		print_digit(n);
	else {
		print_int(n/10);
		print_digit(n%10);
	}
}

void print_fract(double f)
{
	int count = PRECISION - nint;

	if (count > 0) {
		if (f > 0.0f)
			putchar('.');

		while (count-- > 0 && f > 0.0f) {
			f *= 10;
			print_int((ll)f);
			f -= (ll)f;
		}
	}
}

void print(double d)
{
	print_int((ll)d);
	print_fract(d - (ll)d);
}

int main()
{
	double d;

	while (EOF != scanf("%lf", &d)) {
             nint = 0;
		print(d);
		printf(" %.30lf", d);
		putchar('\n');
	}

	return 0;
}
{% endhighlight %}