# jvm基础与配置

## [使用jvisualVm监控本地和远程的jvm](https://blog.csdn.net/wxuan0716/article/details/81538424)



## [使用jvisualvm、jmc远程监控JVM](https://blog.csdn.net/mn960mn/article/details/73958701)

## [java生成dump](https://blog.csdn.net/GLQ_LH/article/details/89202308)



如何产生dump文件
1.JVM的配置文件中配置：

例如：堆初始化大小，而堆最大大小

在应用启动时配置相关的参数 

-XX:+HeapDumpOnOutOfMemoryError，当应用抛出OutOfMemoryError时生成dump文件。

在启动的时候，配置文件在哪个目录下面：

-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=目录+产生的时间.hprof

JVM启动时增加两个参数:

\#出现 OOME 时生成堆 dump:

-XX:+HeapDumpOnOutOfMemoryError

\#生成堆文件地址：

-XX:HeapDumpPath=/home/liuke/jvmlogs/
\2. 发现程序异常前通过执行指令，直接生成当前JVM的dmp文件，6214是指JVM的进程号

jmap -dump:file=文件名.dump [pid]

jmap -dump:format=b,file=serviceDump.dat 6214
由于第一种方式是一种事后方式，需要等待当前JVM出现问题后才能生成dmp文件，实时性不高，第二种方式在执行时，JVM是暂停服务的，所以对线上的运行会产生影响。所以建议第一种方式。

## [jdk工具之JvisualVM、JvisualVM之一--（visualVM介绍及性能分析示例）](https://www.cnblogs.com/duanxz/p/3713773.html)

## [jdk工具之JvisualVM、JvisualVM之二--Java程序性能分析工具Java VisualVM](https://www.cnblogs.com/duanxz/p/3492890.html)

## [jdk命令之Java内存之本地内存分析神器：NMT 和 pmap](https://www.cnblogs.com/duanxz/p/5500494.html)

## [Heap堆分析（堆转储、堆分析）](https://www.cnblogs.com/duanxz/p/8510623.html)

## [heapdump分析简单总结](https://blog.csdn.net/mccxj/article/details/54983022)

