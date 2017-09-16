---
layout: post
title: Broken Stack Guard Page
tags: [cpp, debugging]
---

This week, I updated my [Broken Guard Page](https://github.com/chaelim/BrokenGuardPage) project that includes couple of demo programs. Last demo shows how a malicious program can cause very obscure access violation of other process by just **reading** the memory of the other process. This is one of the examples that how modern software can be fragile and it can affect software reliability.

Most modern programming languages has `function` and if you're a C/C++ programmer, probably already know the different between local variable and dynamically allocated or known as heap memory.

Local variable is usually allocated on thread stack and dynamic allocation uses heap memory. 

In terms of performance, [stack is faster](http://stackoverflow.com/questions/161053/which-is-faster-stack-allocation-or-heap-allocation). It's because not only stack alloc and dealloc operations are faster than heap but also stack allocated memory is **CPU cache friendly** which means the data more likely in the CPU cache and provide much [faster data access](https://gist.github.com/jboner/2841832).
 
Then why don't we use stack allocated variables whenever possible? It's answered here:  [Why is the use of alloca() not considered good practice?](http://stackoverflow.com/questions/1018853/why-is-the-use-of-alloca-not-considered-good-practice) [_alloca](https://msdn.microsoft.com/en-us/library/wb1s57t5.aspx) even accepts size param at run-time. (That's so convenient and also can easily be misused)

Default total thread stack size is [1 MiB](https://msdn.microsoft.com/en-us/library/windows/desktop/ms686774(v=vs.85).aspx) in Visual C++. So if you try to allocate more than 1 MiB on stack, it's guaranteed to cause stack overflow. Stack is also used for other purpose like return address, function parameters and etc. If you have many nested function calls then the actual available stack size can be much smaller.

Windows also has stack expansion at run-time to save physical memory. See [Pushing the Limits of Windows: Processes and Threads](https://blogs.technet.microsoft.com/markrussinovich/2009/07/05/pushing-the-limits-of-windows-processes-and-threads/) for details. Normally thread doesn't use more than couple of hundreds KiB memory, so if OS commits whole 1 MiB for each thread, it'll waste memory. Your system easily running hundreds of threads in total normally. Especially on 32 bit system it was a big deal and even on 64 bit system the default total stack size is still 1 MiB and that seems surprisingly works fine.

