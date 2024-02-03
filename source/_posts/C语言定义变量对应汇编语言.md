---
title: C中的int a = 4;对应的汇编码
date: 2024-01-07 12:00:30
tags:
  - 汇编语言
---



下面的代码对应的汇编是什么呢？

```c
int func() {
	int a == 4;
}
```

接下来，从ubuntu中使用`gcc`命令编译下的结果如下

```sh
gcc -S -fno-asynchronous-unwind-tables demo.c
```

生成的汇编代码

```a
        .file   "demo.c"
        .text
        .globl  func1
        .type   func1, @function
func1:
        endbr64
        pushq   %rbp
        movq    %rsp, %rbp
        movl    $1, -4(%rbp)
        nop
        popq    %rbp
        ret
        .size   func1, .-func1
        .ident  "GCC: (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0"
        .section        .note.GNU-stack,"",@progbits
        .section        .note.gnu.property,"a"
        .align 8
        .long   1f - 0f
        .long   4f - 1f
        .long   5
0:
        .string "GNU"
1:
        .align 8
        .long   0xc0000002
        .long   3f - 2f
2:
        .long   0x3
3:
        .align 8
4:
```

- [汇编语言知识](https://hjlarry.github.io/sicp/asm/)
  - [bak](https://github.com/hjlarry/hjlarry.github.io)

- [Instruction Set Reference, A-Z](https://cdrdv2-public.intel.com/782156/325383-sdm-vol-2abcd.pdf)

以上仅仅是一个简单例子，可以使用上面的方法来看一看c语言中的那些关键字都是怎么实现的。实现方式都是基于CPU中的ISA(Instruction Set Architecture)来实现的。

使用gcc生成汇编代码除了上面的命令还是如下的方法

```shell
gcc -Og -S xxx.c
```

生成`xxx.o`文件

```
gcc -Og -c xxx.c
```

将`xxx.o`文件反汇编成`xxx.s`文件

```
objdump -d xxx.o
```

本文会不断完善，c语言关键字对应的汇编码。
