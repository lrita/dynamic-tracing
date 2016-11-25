# [Performance Analysis Methodology(性能分析方法论)](http://www.brendangregg.com/methodology.html)

## [USE方法](http://www.brendangregg.com/usemethod.html) - 发现资源瓶颈
Utilization Saturation and Errors (USE) 方法，主要面向资源。该方法主要构建一个checklist([LINUX checklist](http://www.brendangregg.com/USEmethod/use-linux.html)、[Rosetta Stone of Performance Checklists](http://www.brendangregg.com/USEmethod/use-rosetta.html))，使用户可以快速的识别出资源瓶颈和错误。





## [TSA方法](http://www.brendangregg.com/tsamethod.html) - 发现应用耗时
Thread State Analysis(TSA) 方法，主要面向线程。用于找到导致线程性能变差的原因。主要思想跟USE一样，只是将对象从资源替换为线程。
```
1. For each thread of interest, measure total time in different thread states.
2. Investigate states from the most to the least frequent, using appropriate tools.
```

## [OFF-CPU分析](http://www.brendangregg.com/offcpuanalysis.html) - 发现等待、延时

## [Active Benchmarking](http://www.brendangregg.com/activebenchmarking.html)
