# perf top
实时分析代码中的热点

## SYNOPSIS
```
perf top [-e <EVENT> | --event=EVENT] [<options>]
```

## OPTIONS
```
  -a, --all-cpus
    System-wide collection. (default)
    监控全部CPU、默认模式

  -c <count>, --count=<count>
    Event period to sample.
    采样周期

  -C <cpu-list>, --cpu=<cpu>
    Monitor only on the list of CPUs provided. Multiple CPUs can be provided as a comma-separated list
    with no space: 0,1. Ranges of CPUs are specified with -: 0-2. Default is to monitor all CPUS.
    指定监控哪些CPU

  -d <seconds>, --delay=<seconds>
    Number of seconds to delay between refreshes.
    刷新间隔

  -e <event>, --event=<event>
    Select the PMU event. Selection can be a symbolic event name (use perf list to list all events) or a
    raw PMU event (eventsel+umask) in the form of rNNN where NNN is a hexadecimal event descriptor.
    指定event

  -E <entries>, --entries=<entries>
    Display this many functions.

  -f <count>, --count-filter=<count>
    Only display functions with more events than this.
    显示采样高于多少的函数

  --group
    Put the counters into a counter group.

  -F <freq>, --freq=<freq>
    Profile at this frequency.

  -i, --inherit
    Child tasks do not inherit counters.
    忽略子进程、线程

  -k <path>, --vmlinux=<path>
    Path to vmlinux. Required for annotation functionality.

  -m <pages>, --mmap-pages=<pages>
    Number of mmap data pages (must be a power of two) or size specification with appended unit character
    - B/K/M/G. The size is rounded up to have nearest pages power of two value.

  -p <pid>, --pid=<pid>
    Profile events on existing Process ID (comma separated list).
    挂载进程ID

  -t <tid>, --tid=<tid>
    Profile events on existing thread ID (comma separated list).
    挂载线程ID

  -u, --uid=
    Record events in threads owned by uid. Name or number.

  -r <priority>, --realtime=<priority>
    Collect data with this RT SCHED_FIFO priority.
    将perf top作为实时任务，优先级为<priority>，保证perf top的采样效率

  --sym-annotate=<symbol>
    Annotate this symbol.

  -K, --hide_kernel_symbols
    Hide kernel symbols.

  -U, --hide_user_symbols
    Hide user symbols.

  --demangle-kernel
    Demangle kernel symbols.

  -D, --dump-symtab
    Dump the symbol table used for profiling.

  -v, --verbose
    Be more verbose (show counter open errors, etc).

  -z, --zero
    Zero history across display updates.

  -s, --sort
    Sort by key(s): pid, comm, dso, symbol, parent, srcline, weight, local_weight, abort, in_tx,
    transaction, overhead, sample, period. Please see description of --sort in the perf-report man page.

  --fields=
    Specify output field - multiple keys can be specified in CSV format. Following fields are available:
    overhead, overhead_sys, overhead_us, overhead_children, sample and period. Also it can contain any
    sort key(s).
    By default, every sort keys not specified in --field will be appended automatically.

  -n, --show-nr-samples
    Show a column with the number of samples.

  --show-total-period
    Show a column with the sum of periods.

  --dsos
    Only consider symbols in these dsos. This option will affect the percentage of the overhead column.
    See --percentage for more info.

  --comms
    Only consider symbols in these comms. This option will affect the percentage of the overhead column.
    See --percentage for more info.

  --symbols
    Only consider these symbols. This option will affect the percentage of the overhead column.

  -M, --disassembler-style=
    Set disassembler style for objdump.

  --source
    Interleave source code with assembly code. Enabled by default, disable with --no-source.

  --asm-raw
    Show raw instruction encoding of assembly instructions.

  -g
    Enables call-graph (stack chain/backtrace) recording.

  --call-graph
    Setup and enable call-graph (stack chain/backtrace) recording, implies -g.

  --children
    Accumulate callchain of children to parent entry so that then can show up in the output. The output
    will have a new "Children" column and will be sorted on the data. It requires -g/--call-graph option
    enabled.

  --max-stack
    Set the stack depth limit when parsing the callchain, anything beyond the specified depth will be
    ignored. This is a trade-off between information loss and faster processing especially for workloads
    that can have a very long callchain stack.
    Default: 127
```

## 常用参数
```
-e: 指定性能事件(默认事件：cycles)
-p: 指定待分析进程的PID
-t: 指定待分析线程的TID
-a: 分析整个系统的性能（Default）
-c: 事件的采样周期
-o: 指定输出文件（Default: perf.data）
-g: 记录函数间的调用关系
-r <priority>: 将perf top作为实时任务，优先级为<priority>，保证perf top的采样效率. Linux中实时优先级的范围是[0,99],其中优先级 0 最高
-u <uid>: 只分析用户<uid>创建的进程
--comms <process name>: 过滤只显示<process name>里的符号
```

## 实例
实时跟踪分析某个进程中消耗CPU最多函数:
执行
```
perf top -p 32188
```
显示
```
11.37%  hund      [.] runtime.scanobject
 7.17%  hund      [.] runtime.heapBitsForObject
 5.31%  hund      [.] runtime.greyobject
 3.77%  hund      [.] runtime.mallocgc
 2.34%  hund      [.] runtime.heapBitsSetType
 1.69%  hund      [.] runtime.mapiternext
 1.55%  hund      [.] runtime.scanblock
 1.50%  hund      [.] runtime.memmove
 1.36%  hund      [.] runtime.gentraceback
 1.35%  hund      [.] runtime/internal/atomic.Or8
 1.26%  hund      [.] runtime.pcvalue
 1.10%  hund      [.] strings.genSplit
 1.07%  hund      [.] encoding/json.(*encodeState).string
 1.06%  hund      [.] runtime.memclr
 [...]
```
根据显示，可以反应每个函数实时消耗CPU的比例，结论`runtime.scanobject`消耗的CPU最多。

将光标移动到`runtime.scanobject`这一行，按`→`键，显示:
```
Annotate runtime.scanobject(显示该函数的汇编代码，同时显示每行汇编的采样率，跟耗时正相关)                                   ◆
Zoom into hund(32188) thread                                                                                      ▒
Zoom into hund DSO(Dynamic Shared Object)                                                                         ▒
Browse map details                                                                                                ▒
Run scripts for samples of thread [hund]                                                                          ▒
Run scripts for samples of symbol [runtime.scanobject]                                                            ▒
Run scripts for all samples                                                                                       ▒
Exit
```

在运行命令，查看调用图谱。
```
perf top -p 32188 -g
```
显示
```
+   50.50%     0.15%  hund              [.] git.intra.weibo.com/platform/hund/pkg/server.(*Server).csumLogLoop
+   43.63%     0.01%  hund              [.] github.com/olivere/elastic.(*BulkService).Do
+   35.73%     0.03%  hund              [.] github.com/olivere/elastic.(*BulkService).bodyAsString
+   34.05%     0.50%  hund              [.] runtime.systemstack
+   32.09%     0.17%  hund              [.] github.com/olivere/elastic.BulkUpdateRequest.Source
+   32.09%     0.08%  hund              [.] github.com/olivere/elastic.(*BulkUpdateRequest).Source
+   27.86%     0.02%  hund              [.] encoding/json.Marshal
+   27.68%     3.98%  hund              [.] runtime.mallocgc
+   27.17%    13.16%  hund              [.] runtime.scanobject
+   27.15%     0.01%  hund              [.] encoding/json.(*encodeState).marshal
+   26.91%     0.09%  hund              [.] encoding/json.(*encodeState).reflectValue
+   26.59%     0.04%  hund              [.] encoding/json.(*mapEncoder).(encoding/json.encode)-fm
+   26.53%     0.45%  hund              [.] encoding/json.(*mapEncoder).encode
+   24.02%     0.10%  hund              [.] encoding/json.interfaceEncoder
+   21.62%     0.01%  hund              [.] github.com/olivere/elastic.(*BulkUpdateRequest).getSourceAsString
[...]
```
这个的输出结果，跟上面的有2点不同，第一点是每行行首多了一个`+`，表示这个函数调用了其他函数，光标移动到该函数上按回车键，可以展开调用:
```
-   42.87%     0.18%  hund              [.] git.intra.weibo.com/platform/hund/pkg/server.(*Server).csumLogLoop    ◆
   - git.intra.weibo.com/platform/hund/pkg/server.(*Server).csumLogLoop                                           ▒
        72.85% runtime.goexit                                                                                     ▒
        12.22% 0                                                                                                  ▒
      + 4.87% 0x20                                                                                                ▒
        3.70% runtime.stackBarrier                                                                                ▒
+   36.23%     0.00%  hund              [.] github.com/olivere/elastic.(*BulkService).Do                          ▒
+   32.61%     0.00%  hund              [.] git.intra.weibo.com/platform/hund/pkg/server.execElasticBulk          ▒
+   28.30%     0.03%  hund              [.] github.com/olivere/elastic.(*BulkService).bodyAsString                ▒
+   27.32%     0.47%  hund              [.] runtime.systemstack
```
第二点不同是，每行前面的采样率变成了累积采样，即A调用了B函数，B的采样率会累加到A上面，这样很容易看出那个函数耗时最长。从上面看出`git.intra.weibo.com/platform/hund/pkg/server.(*Server).csumLogLoop`在第一次调用perf top时，是找不到的，说明它的采样率低于1%，但是在带调用关系的的累积采样率到达了42%，说明了，比较耗时的函数都应该是被`git.intra.weibo.com/platform/hund/pkg/server.(*Server).csumLogLoop`所调用的。应该把`git.intra.weibo.com/platform/hund/pkg/server.(*Server).csumLogLoop`作为优化的总入口。
