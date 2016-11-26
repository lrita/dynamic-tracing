# CPU Flame Graphs
使用`ON-CPU火焰图`，可以迅速找到进程中消耗CPU较高的瓶颈点。为了生成`ON-CPU火焰图`，需要用profile工具收集当前程序的`CPU cycles`、`函数地址或者符号`、`调用栈`等信息。然后使用相关[脚本](https://github.com/brendangregg/FlameGraph)生成对应的火焰图。

## 火焰图解析
- 火焰图中的颜色是随机的，没有任何意义。
- 火焰图中的每一个格子代表一个函数(的栈)`stack frame`。
- Y轴代表函数调用的栈深度(底层的函数调用上层的函数)。最高层的格子代表正在CPU上运行。
- X轴的宽度是采样大小，不是消耗时间。X轴上的格子是按字母排序的。
- X轴的宽度越大的表明消耗CPU的时间越多。
- 总的来说需要注意比较宽大的火苗，特别是类似平顶山的火苗。

## 生成方法
总的来说，生成火焰图只需要三部：
1. 抓取栈信息
2. 折叠栈信息
3. 运行flamegraph.pl脚本

下面简述几种工具的生成方法：

#### perf
```
git clone https://github.com/brendangregg/FlameGraph  # or download it from github
cd FlameGraph
perf record -F 99 -a -g -- sleep 60                   # 可以使用-p/-t参数指定进程/线程
perf script | ./stackcollapse-perf.pl > out.perf-folded
./flamegraph.pl out.perf-folded > perf-kernel.svg
```
在生成中间文件out.perf-folded后，可以从中剔除你不关心的函数栈：
```
perf script | ./stackcollapse-perf.pl > out.perf-folded
grep -v cpu_idle out.perf-folded | ./flamegraph.pl > nonidle.svg
```
也可以只提取出你关心的函数栈：
```
grep ext4 out.perf-folded | ./flamegraph.pl > ext4internals.svg
egrep 'system_call.*sys_(read|write)' out.perf-folded | ./flamegraph.pl > rw.svg
```

#### DTrace
以mysql作为示例
```
git clone https://github.com/brendangregg/FlameGraph  # or download it from github
cd FlameGraph
dtrace -x ustackframes=100 -n 'profile-99 /execname == "mysqld" && arg1/ {
    @[ustack()] = count(); } tick-60s { exit(0); }' -o out.stacks
./stackcollapse.pl out.stacks > out.folded
./flamegraph.pl out.folded > out.svg
```
其中`arg1`是检查是否是用户空间，当它非零时代表用户空间，当`arg0`非零时代表内核空间，例如要抓取内核空间的采样：
```
dtrace -x stackframes=100 -n 'profile-199 /arg0/ {
    @[stack()] = count(); } tick-60s { exit(0); }' -o out.stacks
```

#### SystemTap
```
stap -s 32 -D MAXBACKTRACE=100 -D MAXSTRINGLEN=4096 -D MAXMAPENTRIES=10240 \
    -D MAXACTION=10000 -D STP_OVERLOAD_THRESHOLD=5000000000 --all-modules \
    -ve 'global s; probe timer.profile { s[backtrace()] <<< 1; }
    probe end { foreach (i in s+) { print_stack(i);
    printf("\t%d\n", @count(s[i])); } } probe timer.s(60) { exit(); }' \
    > out.stap-stacks
./stackcollapse-stap.pl out.stap-stacks > out.stap-folded
cat out.stap-folded | ./flamegraph.pl > stap-kernel.svg
```
或者采用春哥的[nginx-systemtap-toolkit](https://github.com/openresty/nginx-systemtap-toolkit#sample-bt)

#### ktap
```
ktap -e 's = ptable(); profile-1ms { s[backtrace(12, -1)] <<< 1 }
    trace_end { for (k, v in pairs(s)) { print(k, count(v), "\n") } }
    tick-30s { exit(0) }' -o out.kstacks
sed 's/	//g' out.kstacks | stackcollapse.pl > out.kstacks.folded
./flamegraph.pl out.kstacks.folded > out.kstacks.svg
```
