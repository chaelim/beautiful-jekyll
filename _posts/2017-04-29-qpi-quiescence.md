---
layout: post
title: QPI Quiescence
tags: [cpu, lock-free]
---

This is sort of note to myself. Here is a good article that I found while ago [QPI Quiescence](https://blogs.oracle.com/dave/qpi-quiescence) from Dave Dice. Again this makes me feel multi-processor or multi-core CPU like a networked system. See [this article](http://queue.acm.org/detail.cfm?id=2492433). Also the article mentioned Atomic [CAS](https://en.wikipedia.org/wiki/Compare-and-swap) is more than 3 times expensive than regular CAS.

[This slide](http://fpl.cs.depaul.edu/jriely/papers/2014-qqc-talk.pdf) summarized and visualized what is **Quiescent Consistency** very well.
> “Method calls separated by a period of quiescence should appear to
> take effect in their real-time order.”
> (Herlihy/Shavit 2008)

**QPI Quiescence** is interesting way to create asymmetric synchronization which is basically optimization for fast-path thread. Idea is eliminating any __#LOCK__ signal from fast-path thread as possible. And the slow-path thread still can use __#LOCK__ signal and also creates **QPI Quiescence**.

Windows provides [FlushProcessWriteBuffers](https://msdn.microsoft.com/en-us/library/windows/desktop/ms683148(v=vs.85).aspx) API which is in fact a mechanism to create **Quiescent Consistency** in muti-processor system. It also recognizes process CPU affinity.
> Flushes the write queue of each processor that is running a thread of the current process.
> The function generates an interprocessor interrupt (IPI) to all processors that are part of the current process affinity. It guarantees the visibility of write operations performed on one processor to the other processors.

Dmitri's [timing result](https://groups.google.com/d/msg/lock-free/yMhzBnaOLMM/cUcLD2k41qsJ) is promising. I even wrote a [Reader/Writer lock](https://github.com/chaelim/RWLock) which is using `FlushProcessWriteBuffers` API before. I'd like to improve the R/W lock with perf comparison with other locks.