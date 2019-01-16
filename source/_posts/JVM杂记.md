---
title: JVM杂记
tags: JVM,Java,监控
date: 2019-1-7 21:56:49
categories: JVM
grammar_cjkRuby: true
---

 1. JVM的参数类型
	 1. 标准参数: -help、-version
	 2. x参数：非标准化参数 -Xint、-Xcomp、Xmixed
	 3. xx参数：
		 * Boolean类型： 格式：-XX[+-]<name> 表示启用或者禁用name属性，比如：-XX:+UseConcMarkSweepGC(CMS垃圾收集器) 或者 -XX:+UseG1GC
		 * 非Boolean类型： 格式：-XX:<name>=<value> 表示name属性的值是value,比如：-XX:MaxGCPauseMillis=500(GC的最大停顿时间是500ms)、XX:GCTimeRatio=19
		 * -Xms等价于-XX:InitialHeapSize(初始化堆大小)、-Xmx等价于-XX:MaxHeapSize(最大的堆的大小)
 2. 运行时的JVM参数的值
	 1. -XX:+PrintFlagsInitial： 
	 2. -XX:+PrintFlagsFinal
	 3. -XX:+UnlockExperimentalVMOptions解锁试验参数
	 4. -XX:+UnlockDiagnosticVMOptions解锁诊断参数
	 5. jinfo
		 *  jinfo -flags 5341
		 *  jinfo -flag MaxHeapSize 5341
![jinfo效果图](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2019/1/201901072237.png)
 3. jstat查看虚拟机信息
	 1. 功能：查看类加载信息、垃圾收集信息、JIT编译信息
	 2. 类加载信息：jstat -class 5341 1000 10

![类加载信息](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2019/1/QQ20190107-0.png)

	3. 垃圾收集信息：-gc、-gcutil、-gccause、-gcnew、-gcold
		* jstat -gc 5341 1000(每1秒钟打印当前的GC信息)

![每秒
GC信息](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2019/1/QQ20190107-1.png)


![JVM的内存机构](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2019/1/QQ20190107-2.png)
非堆区：操作系统的本地内存，独立于JVM的堆区之外。ccs:短指针
	4. JIT编译：-compiler、-printcompilation
 4. jmap+MAT实战内存溢出
	* 内存溢出时候导出内存镜像：-XX:+HeapDumpOnOutOfMemoryError  -XX:HeapDumpPath=./
	* 配置堆区的最大最小的内存：-Xms32M -Xms32M
	* jmap手动导出内存镜像：jmap -dump:format=b,file=heap.hprof 5341
	* jmap -heap 5341
 5. jstack实战死循环和死锁
	 * 使用`jstack pid > pid.txt`,导出当前的进程的信息
	 * 使用`top -p pid -H` ,显示所有的线程的占用资源的情况
	 * 使用`printf "%x" pid`,转化十进制到十六进制