---
layout: post
title:  "Java Virtual Machine - Managed to Native Frame (M2nFrame)"
date:   2015-05-02 16:49
categories: Harmony DRLVM
---

之前一直困惑`M2nFrame`到底是怎么回事，今天偶然间发现了`M2nFrame`结构体的定义，参考给的代码注释，似乎对这个概念明晰了许多。

Java方法被编译后生成的代码被称为Managed Code，表示是受虚拟机管理的代码；对应的，用C/C++编写的代码生成后被称为Native Code （当然这一说法并不完整，用其它语言编写的代码也可以是Native Code，鉴于这种情况不多见，因此本文默认Native Code就是用C/C++编写的代码）。Managed Code的栈帧结构与Native Code的栈帧结构是不一样的，前者为了支持异常处理和垃圾回收，会保存额外的信息，因此，当一个Java方法调用Native Code时，栈帧要作切换，`M2nFrame`结构就记录了发生切换处的栈结构。在DRLVM中，`M2nFrame`的定义如下：

{% highlight c++ %}
/* vmcore/src/lil/ia32/m2n_ia32_internal.h */
struct M2nFrame {
	M2nFrame        *prev_m2nf;
	M2nFrame       **p_lm2nf;
	ObjectHandles   *local_object_handles;
	Method_Handle    method;
	frame_type       current_frame_type;
	Registers       *pop_regs;
	U_32             edi;
	U_32             esi;
	U_32             ebx;
	U_32             ebp;
	U_32             eip;
	Registers       *regs;
};
{% endhighlight %}

举个例子。在DRLVM中，`gc_alloc()`和`gc_alloc_fast()`两个函数作为分配Java对象的接口，`gc_alloc_fast()`声明如下：

{% highlight c++ %}
Managed_Object_handle gc_alloc_fast(unsigned size,
				    Allocation_Handle oh,
				    void *unused_gc_tls);
{% endhighlight %}

按照C语言的函数调用惯例，参数是从右往左入栈的，即进入`gc_alloc_fast()`函数时的栈帧结构应该是这样的（栈向下增长，地址越来越小）：

{% highlight c++ %}
/*

| ...           |
| unused_gc_tls |
| oh            |
| size          |
| eip           | <--- return address of caller
| old ebp       | <--- current ebp,esp

*/
{% endhighlight %}

但是刚才我们说过，由Java方法（即Managed Code）进入Native Code时要发生栈帧结构的变化，因此真实的情况要更复杂。在DRLVM的实现中，即时编译器会生成一个额外的stub（桩）代码块来调用上述的`gc_alloc_fast()`函数，而Java方法会去调用这个stub代码块（Java方法不用理会这些细节，它只需要知道`new`会返回需要的对象）。stub代码块的作用主要就是建立`M2nFrame`，当Java方法调用stub代码块时的栈帧结构是这样的（注意，此时并没有传入参数`unused_gc_tls`）：

{% highlight c++ %}
/*

| ...  |
| oh   |
| size |
| eip  | <--- return address of Java method, also, esp points to here

*/
{% endhighlight %}

接着，stub代码块建立`M2nFrame`，然后重新将Java方法传入的参数`oh`和`size`入栈，并且额外的获取了指向线程局部存储指针`tls`压入栈中：

{% highlight c++ %}
/*

| ...                  |
| oh                   |
| size                 |
| eip                  | <--- return address of Java method
------------------------进入stub代码块----
| ebp                  |
| ebx                  |
| esi                  |
| edi                  |
| pop_regs             |
| current_frame_type   |
| method               |
| local_object_handles |
| p_lm2nf              |
| prev_m2nf            | <--- M2nFrame structure 
| tls                  | 也就是上面gc_alloc_fast()声明中的unused_gc_tls
| oh                   |
| size                 |
| eip                  | <--- return address of stub code
------------------------进入函数gc_alloc_fast()
| ebp                  | <--- current ebp and esp

*/
{% endhighlight %}

有了上述结构，虚拟机就可以在发生异常时进行栈回退（Unwind Stack）操作，识别出栈帧是属于Native Code还是Managed Code。DRLVM提供了如下的接口函数来操作`M2nFrame`结构：

{% highlight c++ %}
/* more functions refers to vmcore/include/m2n.h */

// get the most recent M2nFrame of current thread
M2nFrame *m2n_get_last_frame();

// get the most recent M2nFrame of give thread
M2nFrame *m2n_get_last_frame(VM_thread *);

// get the return address into the preceding managed frame
NativeCodePtr m2n_get_ip(M2nFrame *);

frame_type m2n_get_frame_type(M2nFrame *);

{% endhighlight %}

