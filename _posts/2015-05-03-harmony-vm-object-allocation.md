---
layout: post
title:  "Java Virtual Machine - Object Allocation"
date:   2015-05-03 14:44
categories: Harmony DRLVM
---

DRLVM中对象分配有两大类：数组和非数组对象的分配。两种分配类别又各自有两条分配路径：fast path和slow path。fast path路径上不发生GC，允许返回空值；slow path则可能发生GC，当堆剩余空间不足时，会先GC再分配对象，因此slow path保证不返回空值，若堆内存耗尽则抛出OutOfMemory异常。


非数组对象的分配过程为：

