# perf 中使用的概念

## PMU(Performance Monitoring Unit)
  CPU硬件提供的一些可测量的事件，例如`cache命中`、`CPU周期`、`分支指令`、`TLB重填例外`等等。

## Tracepoints
  `Tracepoint`是散落在内核源代码中的一些 hook，一旦使能，它们便可以在特定的代码被运行到时被触发，这一特性可以被各种 trace/debug 工具所使用。Perf 就是该特性的用户之一。

  假如您想知道在应用程序运行期间，内核内存管理模块的行为，便可以利用潜伏在 slab 分配器中的 tracepoint。当内核运行到这些 tracepoint 时，便会通知 perf。

  perf 将 tracepoint 产生的事件记录下来，生成报告，通过分析这些报告，调优人员便可以了解程序运行时期内核的种种细节，对性能症状作出更准确的诊断。

## DSO 动态共享对象(Dynamic Shared Object)
  perf 中 DSO 共有 5 种类型,分别是:ELF 可执行文件,动态链接库,内核,内核模块,VDSO 等。

## Transaction/Hardware Transaction/Transaction Memory
  Transactional Memory is the concept of using transactions rather than locks to synchronise processes that execute in parallel and share memory.

  At a very simplified level, to synchronise with locks you identify sections of code (called critical sections) that must not be executed simultaneously by different threads and acquire and release locks around the critical sections. Since each lock can only be held by one thread at a time, this guarantees that once one thread enters a critical section, all of the section's operations will have been completed before another thread enters a critical section protected by the same lock(s).

  Transactional memory instead lets you designate sections of code as transactions. The transactional memory system (which can be implemented in hardware, software, or both) then attempts to give you the guarantee that any run of a program in which multiple threads execute transactions in parallel will be equivalent to a different run of the program in which the transactions all executed one after another, never at the same time.

  The transactional memory system does this by allowing transactions to execute in parallel and monitoring their access to transaction variables. If the system detects a conflict between two transactions' access to the same variable, it will cause one of them to abort and "rollback" to the beginning of the transaction it was running; it will then automatically restart the transaction, and the overall state of the system will be as if it had never started the earlier run.

  One goal of transactional memory is ease-of-programming and safety; a properly implemented TM system which is able to enforce that transactions are used correctly gives hard guarantees that there are no parallelism bugs (deadlocks, race conditions, etc) in the program, and only requires that the programmer designate the transactions (and sometimes transaction variables, if the system doesn't just consider all of memory to implicitly be transaction variables), without needing to identify exactly what locks are needed, acquire them in the correct order to prevent deadlock, etc, etc. "Transacitons are used correctly" implies that there is no sharing data between threads without going through transaction variables, no access to transactional data except in transactions, and no "un-rollbackable" operations inside transactions); library based software transactional memory systems for imperative languages like C, Java, etc generally are unable to enforce all of this, which can re-introduce the possibility of some of the parallelism bugs.

  Another goal of transactional memory is increasing parallelism; if you have a whole bunch of parallel operations which access some data structure, all of which might write to it but few of which actually do, then lock-based synchronisation typically requires that all of the operations run serially to avoid the chance of data corruption. Transactional memory would allow almost all of the operations to run in parallel, only losing parallelism when some process actually does write to the data structure.

  In practice (as of when I researched my honours project a few years ago), hardware-based transactional memory hasn't really taken off, and current software transactional memory systems have significant overheads. So software transactional memory is more aimed at "reasonable performance that scales with the available processors moderately well and is pretty easy to code", rather than giving you absolute maximal performance.

  There's a lot of variability between different transactional memory systems though; I'm speaking at quite an abstract and simplified level here.

  referrer: [Understanding Hardware Transactional Memory in Intel's Haswell Architecture](http://www.quepublishing.com/articles/article.aspx?p=2142912)
