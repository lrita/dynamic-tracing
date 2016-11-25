# perf record
`perf record`收集采样信息，并将其记录在数据文件中, 默认在当前目录下生成数据文件：`perf.data`。随后可以通过`perf report`对数据文件进行分析，结果类似于`perf top`。

## SYNOPSIS
```
    perf record [-e <EVENT> | --event=EVENT] [-l] [-a] <command>
    perf record [-e <EVENT> | --event=EVENT] [-l] [-a] — <command> [<options>]
```

## OPTIONS
```
    <command>...
      Any command you can specify in a shell.
      附带记录的命令，含义同perf stat中的command

    -e, --event=
      Select the PMU event. Selection can be:
      ·   a symbolic event name (use perf list to list all events)
      ·   a raw PMU event (eventsel+umask) in the form of rNNN where NNN is a hexadecimal event descriptor.
      ·   a hardware breakpoint event in the form of \mem:addr[:access] where addr is the address in memory
          you want to break in. Access is the memory access type (read, write, execute) it can be passed as
          follows: \mem:addr[:[r][w][x]]. If you want to profile read-write accesses in 0x1000, just set
          mem:0x1000:rw.
      需要监控的event

    --filter=<filter>
      Event filter.

    -a, --all-cpus
      System-wide collection from all CPUs.
      监控全部CPU

    -l
      Scale counter values.

    -p, --pid=
      Record events on existing process ID (comma separated list).
      挂载进程ID

    -t, --tid=
        Record events on existing thread ID (comma separated list). This option also disables inheritance by
        default. Enable it by adding --inherit.
        挂载线程ID

    -u, --uid=
        Record events in threads owned by uid. Name or number.
        监控某个用户ID启动的线程

    -r, --realtime=
        Collect data with this RT SCHED_FIFO priority.
        设置该命令调度的优先级[0,99]，0最高。

    --no-buffering
        Collect data without buffering.

    -c, --count=
        Event period to sample.
        采样间隔，每隔多少个事件点采集一个

    -o, --output=
        Output file name.
        输出文件名

    -i, --no-inherit
        Child tasks do not inherit counters.
        不记录派生线程

    -F, --freq=
        Profile at this frequency.
        指定采样频率，有时默认采样频率不够时，会使数据失真，这事需要增加采样频率。

    -m, --mmap-pages=
        Number of mmap data pages (must be a power of two) or size specification with appended unit character
        - B/K/M/G. The size is rounded up to have nearest pages power of two value.

    -g
        Enables call-graph (stack chain/backtrace) recording.
        画出调用图谱

    --call-graph
        Setup and enable call-graph (stack chain/backtrace) recording, implies -g.

            Allows specifying "fp" (frame pointer) or "dwarf"
            (DWARF's CFI - Call Frame Information) as the method to collect
            the information used to show the call graphs.

            In some systems, where binaries are build with gcc
            --fomit-frame-pointer, using the "fp" method will produce bogus
            call graphs, using "dwarf", if available (perf tools linked to
            the libunwind library) should be used instead.

    -q, --quiet
        Don’t print any message, useful for scripting.

    -v, --verbose
        Be more verbose (show counter open errors, etc).

    -s, --stat
        Per thread counts.

    -d, --data
        Sample addresses.

    -T, --timestamp
        Sample timestamps. Use it with perf report -D to see the timestamps, for instance.

    -n, --no-samples
        Don’t sample.

    -R, --raw-samples
        Collect raw sample records from all opened counters (default for tracepoint counters).

    -C, --cpu
        Collect samples only on the list of CPUs provided. Multiple CPUs can be provided as a comma-separated
        list with no space: 0,1. Ranges of CPUs are specified with -: 0-2. In per-thread mode with
        inheritance mode on (default), samples are captured only when the thread executes on the designated
        CPUs. Default is to monitor all CPUs.
        指定CPU。

    -N, --no-buildid-cache
        Do not update the builid cache. This saves some overhead in situations where the information in the
        perf.data file (which includes buildids) is sufficient.

    -G name,..., --cgroup name,...
        monitor only in the container (cgroup) called "name". This option is available only in per-cpu mode.
        The cgroup filesystem must be mounted. All threads belonging to container "name" are monitored when
        they run on the monitored CPUs. Multiple cgroups can be provided. Each cgroup is applied to the
        corresponding event, i.e., first cgroup to first event, second cgroup to second event and so on. It
        is possible to provide an empty cgroup (monitor all the time) using, e.g., -G foo,,bar. Cgroups must
        have corresponding events, i.e., they always refer to events defined earlier on the command line.

    -b, --branch-any
        Enable taken branch stack sampling. Any type of taken branch may be sampled. This is a shortcut for
        --branch-filter any. See --branch-filter for more infos.

    -j, --branch-filter
        Enable taken branch stack sampling. Each sample captures a series of consecutive taken branches. The
        number of branches captured with each sample depends on the underlying hardware, the type of branches
        of interest, and the executed code. It is possible to select the types of branches captured by
        enabling filters. The following filters are defined:

            ·   any: any type of branches
            ·   any_call: any function call or system call
            ·   any_ret: any function return or system call return
            ·   ind_call: any indirect branch
            ·   u: only when the branch target is at the user level
            ·   k: only when the branch target is in the kernel
            ·   hv: only when the target is at the hypervisor level
            ·   in_tx: only when the target is in a hardware transaction
            ·   no_tx: only when the target is not in a hardware transaction
            ·   abort_tx: only when the target is a hardware transaction abort
            ·   cond: conditional branches

            The option requires at least one branch type among any, any_call, any_ret, ind_call, cond. The
            privilege levels may be omitted, in which case, the privilege levels of the associated event are
            applied to the branch filter. Both kernel (k) and hypervisor (hv) privilege levels are subject to
            permissions. When sampling on multiple events, branch stack sampling is enabled for all the sampling
            events. The sampled branch type is the same for all events. The various filters must be specified as
            a comma separated list: --branch-filter any_ret,u,k Note that this feature may not be available on
            all processors.

    --weight
      Enable weightened sampling. An additional weight is recorded per sample and can be displayed with the
      weight and local_weight sort keys. This currently works for TSX abort events and some memory events
      in precise mode on modern Intel CPUs.

    --transaction
      Record transaction flags for transaction related events.

    --per-thread
      Use per-thread mmaps. By default per-cpu mmaps are created. This option overrides that and uses
      per-thread mmaps. A side-effect of that is that inheritance is automatically disabled. --per-thread
      is ignored with a warning if combined with -a or -C options.

    -D, --delay=
      After starting the program, wait msecs before measuring. This is useful to filter out the startup
      phase of the program, which is often very different.
      命令启动后，延时多长时间再开始监控。

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
-r <priority>: 将perf top作为实时任务，优先级为<priority>
-u <uid>: 只分析用户<uid>创建的进程
-F <count>: 指定采样频率，有时默认采样频率不够时，会使数据失真，这事需要增加采样频率。
```
