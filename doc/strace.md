# strace

## 简介
`strace`常用来跟踪进程执行时的系统调用和所接收的信号。 在Linux世界，进程不能直接访问硬件设备，当进程需要访问硬件设备(比如读取磁盘文件，接收网络数据等等)时，必须由用户态模式切换至内核态模式，通 过系统调用访问硬件设备。strace可以跟踪到一个进程产生的系统调用,包括参数，返回值，执行消耗的时间。

## 安装
1. 下载strace的安装包，地址：[sourceforge](http://jaist.dl.sourceforge.net/project/strace/strace/4.14/strace-4.14.tar.xz)，[github](../bin/strace-4.14.tar.xz)。
2. 解压。
3. `./configure --prefix=/usr`
4. `make && make install`

## 参数
```
strace -h
usage: strace [-CdffhiqrtttTvVwxxy] [-I n] [-e expr]...
              [-a column] [-o file] [-s strsize] [-P path]...
              -p pid... / [-D] [-E var=val]... [-u username] PROG [ARGS]
   or: strace -c[dfw] [-I n] [-e expr]... [-O overhead] [-S sortby]
              -p pid... / [-D] [-E var=val]... [-u username] PROG [ARGS]

Output format:
  -a column      alignment COLUMN for printing syscall results (default 40) (设置返回值的输出位置.默认 为40)
  -i             print instruction pointer at time of syscall (输出系统调用的入口指针)
  -o file        send trace output to FILE instead of stderr (将结果输出到指定文件)
  -q             suppress messages about attaching, detaching, etc.
  -r             print relative timestamp (打印出相对时间关于,,每一个系统调用)
  -s strsize     limit length of print strings to STRSIZE chars (default 32) (指定输出的字符串的最大长度.默认为32.文件名一直全部输出)
  -t             print absolute timestamp (在输出中的每一行前加上时间信息)
  -tt            print absolute timestamp with usecs (在输出中的每一行前加上时间信息,微秒级)
  -T             print time spent in each syscall (显示每一调用所耗的时间)
  -x             print non-ascii strings in hex (以十六进制形式输出非标准字符串)
  -xx            print all strings in hex (所有字符串以十六进制形式输出)
  -y             print paths associated with file descriptor arguments
  -yy            print protocol specific information associated with socket file descriptors

Statistics:
  -c             count time, calls, and errors for each syscall and report summary(统计每一系统调用的所执行的时间,次数和出错的次数等.)
  -C             like -c but also print regular output
  -O overhead    set overhead for tracing syscalls to OVERHEAD usecs
  -S sortby      sort syscall counts by: time, calls, name, nothing (default time)
  -w             summarise syscall latency (default is system time)

Filtering:
  -e expr        a qualifying expression: option=[!]all or option=[!]val1[,val2]...
     options:    trace, abbrev, verbose, raw, signal, read, write

(指定一个表达式,用来控制如何跟踪.格式如下:
[qualifier=][!]value1[,value2]...
qualifier只能是 trace,abbrev,verbose,raw,signal,read,write其中之一.value是用来限定的符号或数字.默认的 qualifier是 trace.感叹号是否定符号.例如:
-e open等价于 -e trace=open,表示只跟踪open调用.而-e trace!=open表示跟踪除了open以外的其他调用.有两个特殊的符号 all 和 none.
注意有些shell使用!来执行历史记录里的命令,所以要使用.
-e trace=set
只跟踪指定的系统 调用.例如:-e trace=open,close,rean,write表示只跟踪这四个系统调用.默认的为set=all.
-e trace=file
只跟踪有关文件操作的系统调用.
-e trace=process
只跟踪有关进程控制的系统调用.
-e trace=network
跟踪与网络有关的所有系统调用.
-e strace=signal
跟踪所有与系统信号有关的 系统调用
-e trace=ipc
跟踪所有与进程通讯有关的系统调用
-e abbrev=set
设定 strace输出的系统调用的结果集.-v 等与 abbrev=none.默认为abbrev=all.
-e raw=set
将指 定的系统调用的参数以十六进制显示.
-e signal=set
指定跟踪的系统信号.默认为all.如 signal=!SIGIO(或者signal=!io),表示不跟踪SIGIO信号.
-e read=set
输出从指定文件中读出 的数据.例如:
-e read=3,5
-e write=set
输出写入到指定文件中的数据.
)

  -P path        trace accesses to path

Tracing:
  -b execve      detach on execve syscall
  -D             run tracer process as a detached grandchild, not as parent
  -f             follow forks (跟踪由fork调用所产生的子进程)
  -ff            follow forks with output into separate files (如果提供-o filename,则所有进程的跟踪结果输出到相应的filename.pid中,pid是各进程的进程号)
  -I interruptible
     1:          no signals are blocked
     2:          fatal signals are blocked while decoding syscall (default)
     3:          fatal signals are always blocked (default if '-o FILE PROG')
     4:          fatal signals and SIGTSTP (^Z) are always blocked
                 (useful to make 'strace -o FILE PROG' not stop on ^Z)

Startup:
  -E var         remove var from the environment for command
  -E var=val     put var=val in the environment for command
  -p pid         trace process with process id PID, may be repeated
  -u username    run command as username handling setuid and/or setgid

Miscellaneous:
  -d             enable debug output to stderr (输出strace关于标准错误的调试信息)
  -v             verbose mode: print unabbreviated argv, stat, termios, etc. args (输出所有的系统调用.一些调用关于环境变量,状态,输入输出等调用由于使用频繁,默认不输出)
  -h             print help message
  -V             print version
```

## 案例1
查看一个程序每个阶段的耗时，`-r`采用相对时间戳，看进程从开始到结束，耗时分布。
```
     strace -r ls > /dev/null
```
输出
```
     0.000000 execve("/usr/bin/ls", ["ls"], [/* 27 vars */]) = 0
     0.000687 brk(NULL)                 = 0xc4c000
     0.000034 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f937ef24000
     0.000032 access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory)
     0.000045 open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
     0.000024 fstat(3, {st_mode=S_IFREG|0644, st_size=26364, ...}) = 0
     0.000023 mmap(NULL, 26364, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f937ef1d000
     0.000015 close(3)                  = 0
     0.000019 open("/lib64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
     0.000021 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\24
     .................
     0.000018 close(1)                  = 0
     0.000013 munmap(0x7f937ef23000, 4096) = 0
     0.000021 close(2)                  = 0
     0.000030 exit_group(0)             = ?
     0.000395 +++ exited with 0 +++
```

## [案例2](http://huoding.com/2013/10/06/288)
俗话说：不怕贼偷，就怕贼惦记着。在面对故障的时候，我也有类似的感觉：不怕出故障，就怕你不知道故障的原因，故障却隔三差五的找上门来。


十一长假还没结束，服务器却频现高负载，Nginx出现错误日志：
```
connect() failed (110: Connection timed out) while connecting to upstream
connect() failed (111: Connection refused) while connecting to upstream
```
看上去是Upstream出了问题，在本例中Upstream就是PHP（版本：5.2.5）。可惜监控不完善，我搞不清楚到底是哪出了问题，无奈之下只好不断重启PHP来缓解故障。


如果每次都手动重启服务无疑是个苦差事，幸运的是可以通过CRON设置每分钟执行：
```
#/bin/bash

LOAD=$(awk '{print $1}' /proc/loadavg)

if [ $(echo "$LOAD > 100" | bc) = 1 ]; then
    /etc/init.d/php-fpm restart
fi
```
可惜这只是一个权宜之计，要想彻底解决就必须找出故障的真正原因是什么。
闲言碎语不要讲，轮到Strace出场了，统计一下各个系统调用的耗时情况：
```
shell> strace -c -p $(pgrep -n php-cgi)
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 30.53    0.023554         132       179           brk
 14.71    0.011350         140        81           mlock
 12.70    0.009798          15       658        16 recvfrom
  8.96    0.006910           7       927           read
  6.61    0.005097          43       119           accept
  5.57    0.004294           4       977           poll
  3.13    0.002415           7       359           write
  2.82    0.002177           7       311           sendto
  2.64    0.002033           2      1201         1 stat
  2.27    0.001750           1      2312           gettimeofday
  2.11    0.001626           1      1428           rt_sigaction
  1.55    0.001199           2       730           fstat
  1.29    0.000998          10       100       100 connect
  1.03    0.000792           4       178           shutdown
  1.00    0.000773           2       492           open
  0.93    0.000720           1       711           close
  0.49    0.000381           2       238           chdir
  0.35    0.000271           3        87           select
  0.29    0.000224           1       357           setitimer
  0.21    0.000159           2        81           munlock
  0.17    0.000133           2        88           getsockopt
  0.14    0.000110           1       149           lseek
  0.14    0.000106           1       121           mmap
  0.11    0.000086           1       121           munmap
  0.09    0.000072           0       238           rt_sigprocmask
  0.08    0.000063           4        17           lstat
  0.07    0.000054           0       313           uname
  0.00    0.000000           0        15         1 access
  0.00    0.000000           0       100           socket
  0.00    0.000000           0       101           setsockopt
  0.00    0.000000           0       277           fcntl
------ ----------- ----------- --------- --------- ----------------
100.00    0.077145                 13066       118 total
```
看上去「brk」非常可疑，它竟然耗费了三成的时间，保险起见，单独确认一下：
```
shell> strace -T -e brk -p $(pgrep -n php-cgi)
brk(0x1f18000) = 0x1f18000 <0.024025>
brk(0x1f58000) = 0x1f58000 <0.015503>
brk(0x1f98000) = 0x1f98000 <0.013037>
brk(0x1fd8000) = 0x1fd8000 <0.000056>
brk(0x2018000) = 0x2018000 <0.012635>
```
说明：在Strace中和操作花费时间相关的选项有两个，分别是「-r」和「-T」，它们的差别是「-r」表示相对时间，而「-T」表示绝对时间。简单统计可以用「-r」，但是需要注意的是在多任务背景下，CPU随时可能会被切换出去做别的事情，所以相对时间不一定准确，此时最好使用「-T」，在行尾可以看到操作时间，可以发现确实很慢。

在继续定位故障原因前，我们先通过「man brk」来查询一下它的含义：


*brk() sets the end of the data segment to the value specified by end_data_segment, when that value is reasonable, the system does have enough memory and the process does not exceed its max data size (see setrlimit(2)).*


简单点说就是内存不够用时通过它来申请新内存（data segment），可是为什么呢？
```
shell> strace -T -p $(pgrep -n php-cgi) 2>&1 | grep -B 10 brk
stat("/path/to/script.php", {...}) = 0 <0.000064>
brk(0x1d9a000) = 0x1d9a000 <0.000067>
brk(0x1dda000) = 0x1dda000 <0.001134>
brk(0x1e1a000) = 0x1e1a000 <0.000065>
brk(0x1e5a000) = 0x1e5a000 <0.012396>
brk(0x1e9a000) = 0x1e9a000 <0.000092>
```
通过`grep`我们很方便就能获取相关的上下文，反复运行几次，发现每当请求某些PHP脚本时，就会出现若干条耗时的`brk`，而且这些PHP脚本有一个共同的特点，就是非常大，甚至有几百K，为何会出现这么大的PHP脚本？实际上是程序员为了避免数据库操作，把非常庞大的数组变量通过`var_export`持久化到PHP文件中，然后在程序中通过`include`来获取相应的变量，因为变量太大，所以PHP不得不频繁执行`brk`，不幸的是在本例的环境中，此操作比较慢，从而导致处理请求的时间过长，加之PHP进程数有限，于是乎在Nginx上造成请求拥堵，最终导致高负载故障。


下面需要验证一下推断似乎否正确，首先查询一下有哪些地方涉及问题脚本：
```
shell> find /path -name "*.php" | xargs grep "script.php"
```
直接把它们都禁用了，看看服务器是否能缓过来，或许大家觉得这太鲁蒙了，但是特殊情况必须做出特殊的决定，不能像个娘们儿似的优柔寡断，没过多久，服务器负载恢复正常，接着再统计一下系统调用的耗时：
```
shell> strace -c -p $(pgrep -n php-cgi)
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 24.50    0.001521          11       138         2 recvfrom
 16.11    0.001000          33        30           accept
  7.86    0.000488           8        59           sendto
  7.35    0.000456           1       360           rt_sigaction
  6.73    0.000418           2       198           poll
  5.72    0.000355           1       285           stat
  4.54    0.000282           0       573           gettimeofday
  4.41    0.000274           7        42           shutdown
  4.40    0.000273           2       137           open
  3.72    0.000231           1       197           fstat
  2.93    0.000182           1       187           close
  2.56    0.000159           2        90           setitimer
  2.13    0.000132           1       244           read
  1.71    0.000106           4        30           munmap
  1.16    0.000072           1        60           chdir
  1.13    0.000070           4        18           setsockopt
  1.05    0.000065           1       100           write
  1.05    0.000065           1        64           lseek
  0.95    0.000059           1        75           uname
  0.00    0.000000           0        30           mmap
  0.00    0.000000           0        60           rt_sigprocmask
  0.00    0.000000           0         3         2 access
  0.00    0.000000           0         9           select
  0.00    0.000000           0        20           socket
  0.00    0.000000           0        20        20 connect
  0.00    0.000000           0        18           getsockopt
  0.00    0.000000           0        54           fcntl
  0.00    0.000000           0         9           mlock
  0.00    0.000000           0         9           munlock
------ ----------- ----------- --------- --------- ----------------
100.00    0.006208                  3119        24 total
```
显而易见，`brk`已经不见了，取而代之的是`recvfrom`和`accept`，不过这些操作本来就是很耗时的，所以可以定位「brk」就是故障的原因。
