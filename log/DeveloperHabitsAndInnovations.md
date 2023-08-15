#### 1、Idea配置
Help -> Edit Custom VM Options

~~~

-Xms1024m
-Xmx2048m
-XX:+UseConcMarkSweepGC
-Djava.net.preferIPv4Stack=true
-Dfile.encoding=UTF-8

~~~

解释：

> 调整 Java 虚拟机（JVM）的运行时配置的命令行参数。它们会影响 JVM 的内存分配、垃圾回收、网络设置和文件编码等方面。

* `-Xms1024m`：设置 JVM 的初始堆内存大小。在这个例子中，JVM 初始时会分配 1024MB（1GB）的堆内存。堆内存用于存放对象实例。

* `-Xmx2048m`：设置 JVM 的最大堆内存大小。JVM 运行时的堆内存大小不会超过这个限制。在这个例子中，JVM 最大可以分配 2048MB（2GB）的堆内存。

* `-XX:+UseConcMarkSweepGC`：指示 JVM 使用并发标记-清除（Concurrent Mark-Sweep，CMS）垃圾回收器。CMS 垃圾回收器在尽量减少应用程序停顿的同时，进行垃圾回收。

* `-Djava.net.preferIPv4Stack=true`：设置 JVM 使用 IPv4 协议栈。在某些情况下，IPv6 可能会导致网络连接问题，通过这个参数设置，可以强制使用 IPv4。

* `-Dfile.encoding=UTF-8`：设置 JVM 的文件编码为 UTF-8。这会影响到程序在读写文件时使用的字符编码。

这些参数可以在启动 Java 应用程序时通过命令行传递给 JVM，用于调整应用程序的性能、内存分配和行为。不同的应用程序可能需要根据其特定需求来配置这些参数。需要注意的是，这些参数的具体效果和行为可能因不同的 Java 版本、操作系统和应用程序而有所不同。

