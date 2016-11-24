# perf: Linux profiling with performance counters

## 介绍
Perf是内置于Linux内核源码树中的性能剖析(profiling)工具。它基于事件采样原理，以性能事件为基础，支持针对处理器相关性能指标与操作系统相关性能指标的性能剖析。常用于性能瓶颈的查找与热点代码的定位。

通过它，应用程序可以利用`PMU`，`tracepoint`和内核中的特殊计数器来进行性能统计。它不但可以分析指定应用程序的性能问题(per thread)，也可以用来分析内核的性能问题，当然也可以同时分析应用代码和内核，从而全面理解应用程序中的性能瓶颈。

最初的时候，它叫做`Performance counter`，在 2.6.31 中第一次亮相。此后他成为内核开发最为活跃的一个领域。在 2.6.32 中它正式改名为 `Performance Event`，因为`perf`已不再仅仅作为`PMU`的抽象，而是能够处理所有的性能相关的事件。

使用 perf，您可以分析程序运行期间发生的硬件事件，比如 `instructions retired`，`processor clock cycles`等；您也可以分析软件事件，比如`Page Fault`和进程切换。

这使得`Perf`拥有了众多的性能分析能力，举例来说，使用`Perf`可以计算每个时钟周期内的指令数，称为`IPC`，`IPC`偏低表明代码没有很好地利用CPU。`Perf`还可以对程序进行函数级别的采样，从而了解程序的性能瓶颈究竟在哪里等等。`Perf`还可以替代`strace`，可以添加动态内核 probe 点，还可以做 benchmark 衡量调度器的好坏...

## 工具集
perf 一共有22个工具集:
```
annotate        Read perf.data (created by perf record) and display annotated code
archive         Create archive with object files with build-ids found in perf.data file
bench           General framework for benchmark suites
buildid-cache   Manage build-id cache.
buildid-list    List the buildids in a perf.data file
diff            Read perf.data files and display the differential profile
evlist          List the event names in a perf.data file
inject          Filter to augment the events stream with additional information
kmem            Tool to trace/measure kernel memory(slab) properties
kvm             Tool to trace/measure kvm guest os
list            List all symbolic event types
lock            Analyze lock events
mem             Profile memory accesses
record          Run a command and record its profile into perf.data
report          Read perf.data (created by perf record) and display the profile
sched           Tool to trace/measure scheduler properties (latencies)
script          Read perf.data (created by perf record) and display trace output
stat            Run a command and gather performance counter statistics
test            Runs sanity tests.
timechart       Tool to visualize total system behavior during a workload
top             System profiling tool.
trace           strace inspired tool
probe           Define new dynamic tracepoints
```

## Wiki
  [官方Wiki](https://perf.wiki.kernel.org/index.php/Main_Page)
