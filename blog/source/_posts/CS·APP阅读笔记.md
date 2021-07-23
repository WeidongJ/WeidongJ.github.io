---
title: CS·APP阅读笔记
date: 2021-04-25 17:20:14
tags:
description:  Computer Systems. A Programmer’s Perspective 阅读笔记
---

这是21年计划看完的第一本书，之前一直看的断断续续，现在整体的先读一遍，后续也会读几遍。

## 第三章 Machin-Level Represtentation of Programs

### 概述

### Program Encoding

当我们使用Unix命令行编译C源文件时，参考如下代码：

``` shell command
linux> gcc -Og -o p p1.c p2.c
```

GCC编译过程大致如下：

1. 扩展源文件，将所有 `#include`和`#define`声明的文件和宏加载进来。
2. 编译器生成名为 `sp1.s`和`p2.s` 的两个源文件的汇编代码版本
3. 汇编器将汇编代码转换为二进制对象代码文件`sp1.o`和`p2.o`(对象代码文件也是一种包含所有二进制指令的机器码，但是其内所有的全局变量还没有填充)。
4. 最后，链接器将这两个目标代码文件与实现库函数的代码（例如，printf）合并，并生成最终的可执行代码文件 `p`

#### 机器码

机器码有2个重要的组成部分，指令集（ISA）和虚拟地址。指令集定义了处理器的状态、指令的的格式以及每个指令对统计数据的影响。虚拟地址是一个大的字节数组，它实际是由硬件和操作系统的组合。
处理器状态的一部分是可见的，大部分是对程序员隐藏的

* 程序计数器（program counter）
