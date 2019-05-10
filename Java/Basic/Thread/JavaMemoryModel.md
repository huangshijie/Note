# Java 内存模型

Java内存模型(Java Memory Model, JMM)与Java内存区域中的Java堆、栈、方法区等不是同一个层次的内存划分，两者基本是没有关系的。
硬要扯上关系的话，从变量、主内存、工作内存的定义来看，主内存主要对应于Java堆中的对象实例数据部分(除了实例数据，Java堆还保存了对象的其他信息)，而工作内存则对应于虚拟机栈中的部分区域。

从更低的层次上说，主内存就直接对应于物理硬件的内存，而为了获得更好的运行速度，虚拟机可能会让工作内存优先存储于寄存器和高速缓存中，因为程序运行时主要访问读写的是工作内存。