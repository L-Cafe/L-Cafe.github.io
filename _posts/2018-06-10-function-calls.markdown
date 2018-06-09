---
title:      "x86 Function call conventions"
date:       2018-06-10 10:57:04 +0100
tags:       software-rev assembly x86
category:   Cybersecurity
comment:    header sizes are 2000x750
header:
  image:	/assets/images/2018-06-10-function-calls/header.jpg
  teaser:	/assets/images/2018-06-10-function-calls/header.jpg
  caption:	"Photo credit: [**Unsplash**](https://unsplash.com)"
published: false
---

Another cybersecurity post. Focused on binary (dis)assembly.

This time it's about how different compilers turn high-level programming language code into low-level assembly code, in regards to function calling.

As a sample, I'll use a pseudocode call like `test(a,b,c,d)` to demonstrate how it would look like in regards to different conventions.

Keep in mind `push` is a pseudoinstruction that subtracts 4 from the stack pointer (ESP), and then writes the value onto the memory ESP points to. `pop` is the opposite: reads from the address ESP points to, and then adds 4.

# cdecl

It's used by many C compilers.

In Linux, the GNU Compiler Collection (GCC) sets the cdecl de facto standard.

## Conditions

The registers ST0 through ST7 are empty when calling a function.

ST1 through ST7 are empty upon exiting a function, and if no floating point is returned, ST(0) is empty too.

## Arguments and return values

Arguments are passed on the stack, right-to-left (C style).

For returns, integers and memory addresses are in EAX, whereas floating points will be in the ST0 x87 register.

## Clean-up

Registers EAX, ECX and EDX are caller-saved, and the rest are callee-saved.

## Pseudocode assembly

```nasm
push  d
push  c
push  b
push  a
call  test
```

The result will be in EAX, and floating point values in ST0.

# syscall

Similar to cdecl.

It's the convention for the 32 bit OS/2 API.

## Arguments and return values

Arguments are passed on the stack, right-to-left (C style). The size of the parameter list in doublewords is passed in AL.

Return values are in EAX.

## Clean-up

ECX, and EDX are cleared upon exiting the function call.

## Pseudocode assembly

```nasm
mov   4, al
push  d
push  c
push  b
push  a
call  test
```

The result will be in EAX, and floating point values in ST0.

# Microsoft fastcall

This convention is better than the previous two in terms of performance, hence its "fast" prefix.

However, the fastcall convention is not standarised, and varies a lot from compiler to compiler, so I'll focus in the Microsoft implementation. In this case, GCC's `__msfastcall` is similar.

## Arguments and return values

First two arguments are passed left-to-right into ECX and EDX. The remaining arguments are pushed onto the stack from right to left.

Microsoft compilers behave differently for IA64 and AMD64 architectures, because of additional registers. They simply use their own x64 calling convention, regardless of what was specified to the compiler.

## Clean-up

The callee is responsible for cleaning the stack.

## Pseudocode assembly

```nasm
mov   a, ecx
mov   b, edx
push  d
push  c
call  test
```

The result will be in EAX, and floating point values in ST0.
