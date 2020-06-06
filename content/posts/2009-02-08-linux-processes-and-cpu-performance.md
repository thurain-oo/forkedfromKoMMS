---
title: Linux Processes and CPU Performance
author: minn
type: post
date: 2009-02-08T12:56:01+00:00
url: /2009/02/08/linux-processes-and-cpu-performance/
keywords:
  - linux cpu processes vmstat top command
categories:
  - Linux
tags:
  - cpu
  - Linux
  - processes
  - scheduler
  - top
  - vmstat

---
In Linux, a process can be either:

  * runnable, or
  * blocked (awaiting some events to complete)

When it&#8217;s **runnable**, the process is in competition with other processes for CPU time. A runnable process may or may not be consuming CPU time. It is the **CPU scheduler** that decides which process to run next from the runnable processes list. The processes form a line, known as **run queue**, when they are waiting to use the CPU.

When it&#8217;s **blocked**, it may mean it&#8217;s waiting for data from IO device or the results of a system call.

System usually shows the **load** by totalling the running processes and the runnable processes.

**Multitasking**
  
When it comes to multitasking, the OS can be:

  * cooperative multitasking, or
  * preemptive multitasking

In **preemptive multitasking**, scheduler gives the processes time slices for CPU. The process will be involuntarily suspended after it has consumes the allocated time. It prevents one process from monopolizing the available CPU time.

In **cooperative multitasking**, the process will not stop running until it is voluntary. When it suspends itself, it is called **yielding**. The scheduler cannot make decision how long the process should run.

**Scheduler**
  
Starting from kernel 2.5, Linux gets itself a new scheduler, O(1). Now it&#8217;s been replaced with CFS, as I&#8217;ve written about it in my earlier posts.

**Tools to view the CPU performance
  
** I usually use these tools to check:

  * vmstat
  * top

Those tools are quite basic, yet are able to produce pretty good information, and they come with almost every distro.

**vmstat**, I would check the number interrupts fired (in), the number of context switches (cs), as well as CPU utilization such as User (us), System (sy), Idle (id). I expect to see lower &#8220;cs&#8221; than &#8220;in&#8221;. I&#8217;ll try to explain the context switches and the interrupts in my future posts. For the time being, kindly google for them.

**top**, version 3 produces more stats. We can check the states of the processes, as well as the user cpu stats, system cpu stats (softirq, iowait, irq).