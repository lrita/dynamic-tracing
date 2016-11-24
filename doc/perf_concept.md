# perf 中使用的概念

## PMU(Performance Monitoring Unit)
  CPU硬件提供的一些可测量的事件，例如`cache命中`、`CPU周期`、`分支指令`、`TLB重填例外`等等。

## Tracepoints
  `Tracepoint`是散落在内核源代码中的一些 hook，一旦使能，它们便可以在特定的代码被运行到时被触发，这一特性可以被各种 trace/debug 工具所使用。Perf 就是该特性的用户之一。

  假如您想知道在应用程序运行期间，内核内存管理模块的行为，便可以利用潜伏在 slab 分配器中的 tracepoint。当内核运行到这些 tracepoint 时，便会通知 perf。

  perf 将 tracepoint 产生的事件记录下来，生成报告，通过分析这些报告，调优人员便可以了解程序运行时期内核的种种细节，对性能症状作出更准确的诊断。

## DSO 动态共享对象(Dynamic Shared Object)
  perf 中 DSO 共有 5 种类型,分别是:ELF 可执行文件,动态链接库,内核,内核模块,VDSO 等。
