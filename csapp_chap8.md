---
title: "CSAPP第八章笔记之异常控制流"
date: 2022-01-27T22:21:28+08:00
author : "Jun"
categories: ["编程"]
taggs: ["CSAPP"]
---

# 异常控制流
- 硬件层上，硬件检测到的事件会触发控制突然转移到异常控制程序。
- 操作系统层面上，内核通过上下文切换将控制从一个用户进程转移到另一个用户进程。
- 应用层上，一个进程可以通过发送信号到另一个进程，将控制转移到一个信号处理程序中。
- 一个程序也可以回避正常的栈规则，使用`setjmp`等函数执行到其他函数中的任意位置。

硬件上的异常不做详细讨论。

## 异常处理
系统中可能的每种类型的异常都分配了一个唯一的异常号，在操作系统在启动时会分配和初始化一个异常表。这个异常表储存了一系列的函数指针，当发生一个事件，并且确定了相应的异常号k时，处理器就会触发异常，通过异常表的条目k，转到相应的处理程序。

### 异常的类型

|      类别       |       原因        | 方式 |       返回行为       |
| :-------------: | :---------------: | :--: | :------------------: |
| 中断(interrupt) | 来自I/O设备的信号 | 异步 | 总是返回到下一条指令 |
|   陷阱(trap)    |    有意的信号     | 同步 | 总是返回到下一条指令 |
|   故障(fault)   | 潜在可恢复的信号  | 同步 | 可能返回到下一条指令 |
|   中止(abort)   |  不可恢复的信号   | 同步 |       不会返回       |



### Linux/x86-64中的异常

在x86系统中，总共有256种不同的异常类型。从0~31为Intel定义的异常，32~255为特定的系统定义的中断和陷阱。Linux提供了几百种系统调用，当应用程序想要请求内核服务时便可以使用，比如读写文件或者创建一个新的进程。每个系统调用都有一个确定的编号，这个编号对应到一个内核中跳转表的偏移量。（注意这个跳转表和异常表不同）

C程序可以用syscall函数实现任何系统调用。

```c
 long syscall(long number, ...);
```

但实际上每个系统调用都有自己专门封装的函数，`syscall`相当于提供了一个更通用的方式执行系统调用。而从指令集的角度上说，系统调用可以通过`syscall`指令实现。其中：

- 寄存器`%rax`包含系统调用号
- 寄存器如`%rdi`,`%rsi`,`%rdx`等传递其他的参数

## 进程
进程的经典定义是一个执行中的程序实例。系统中的每个程序都运行在某个进程的上下文中。上下文主要指一个程序正常运行所需要的一些资源，比如：存放在内存中的程序代码和数据，寄存器和程序计数器中的内容等。

### 用户模式和内核模式
处理器通常使用某一个控制寄存器的一个模式位来确定当前进程是否在内核态或者用户态。Linux系统中，`/proc`文件系统将许多内核数据结构的内容输出为一个人类可读的文本。这样我们便可以以此找出一般的系统属性，比如CPU类型或者某个进程所使用的内存段。

### 上下文切换
在进程执行的某个状态下，内核可以决定抢占当前进程，并重新开始一个之前被抢占的进程，这个过程被称为调度。下面是两种可能触发上下文切换的情况：
- 当内核代表用户执行系统调用时。如果系统调用因为等待某个事件发生而阻塞，那么内核可以休眠当前进程而切换到另一个进程。
- 中断。所有的系统都有某种产生定时器中断的机制，通常为1ms或者10ms。每次发生定时器中断时，内核就能判定当前进程是否已经存在了足够时间，并切换到另一个进程。

###  创建进程
父进程可以调用`fock()`函数创建一个子进程。子进程得到与父进程完全一样的虚拟地址空间副本，且共享父进程所有打开的文件。（因为得到了父进程打开的文件描述符的副本）

有几点要格外注意：
- 注意`fork()`被调用了一次但却会返回两次。在父进程中返回子进程的PID，在子进程中返回0。
- 父子进程是并发执行的。这意味着内核可以按自己的意愿以任意顺序执行两个进程，这是我们无法预料的。

### 回收子进程
当一个进程终止时，内核不会立刻将它清除。该进程进入僵死进程状态，直至被其父进程回收。父进程回收已终止的进程时，内核将子进程的退出状态传递给父进程，然后抛弃已终止的进程。

如果父进程终止了，便称其为孤儿进程，内核会安排`init`进程成为它的养父进程。

### 加载运行程序
我们可以使`execve`函数在当前进程的上下文加载并运行一个新程序。它的原型为：
```c
int execve(const char *filename, const char *argcv[], const char 					*envp[]);
```
`execve`会加载并运行可执行文件filename，且带参数列表argcv和环境变量envp。注意`execve`会覆盖当前进程的地址空间。

## 信号
一个信号就是一条信息，它通知进程系统中发生了一个某种类型的事件。每种信号都对应了某种系统事件，正常情况下用户进程是无法看到底层的内核异常的，而信号则为通知用户进程提供了一种方式。信号一般由内核发送给进程，依据进程中发生的异常，比如进程尝试非法引用内存，内核就会发送它一个`SIGSEGV`信号。

### 信号工作的基本流程
内核通过更新目的进程上下文中的某个状态，发送一个信号给目的进程。目的进程必须接受发出的信号，并作出以下几种可能的反应：
- 忽略它
- 终止进程
- 执行信号处理程序(signal handler)

### 发送信号

- 通过`kill`命令发送信号。`kill`命令并不总是意味着杀死某个进程，而是被用来发送信号。比如：
```bash
$ kill -9 15213
```
这意味着向PID为15213的进程发送9号信号，即`SIGKILL`。

```bash
kill -9 -15213
````
这意味着向进程组为15213的所有进程发送9号信号，即`SIGKILL`。

- 通过键盘发送信号。在键盘上使用CTRL+C会导致内核发送`SIGINT`信号到前台进程组中的每个进程。而输入CTRL+Z会发送一个`SIGTSTP`信号到前台进程组中的每个进程，结果是挂起前台作业。

- 用`kill`函数发送信号。与`kill`命令类似但是是库函数。函数原型如下：
```c
int kill(pid_t pid, int sig);
```

- 用`alarm`函数发送信号。进程可以调用`alarm`函数向自己发送`SIGALRM`信号。
