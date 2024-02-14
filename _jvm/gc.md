# 垃圾回收相关
> 垃圾回收器及算法

## GC对象搜索算法

#### **引用计数法**

  这个算法非常简单。给对象一个计数器，每当这个对象被引用了，计数器值加1；引用失效，则减1。当这个对象计数值为0的时候，证明是无用对象，可以被GC程序回收掉。
  但是引用计数算法**无法解决对象间相互引用的问题**。当a对象引用了b对象，b对象也引用了a对象，这样a、b两个对象的计数器值都不会为0，即使这两个对象都没有被其他对象所引用，最终导致这些对象一直无法被回收。这种情况往往会出现在比较复杂的编程语言中。

#### **可达性分析算法**

  可达性分析算法（GC roots算法），广泛应用于主流的商用语言。设置一个根节点，从图论角度来看，只要从该节点可达一个对象，证明这个对象是存活的（被引用）。

  通常地，GC会包含以下区域的对象：

   * 虚拟机栈（栈帧中的 **本地变量** 表）中引用的对象；
   * 方法区中 **静态属性** 引用的对象；
   * 方法区中 **常量** 引用的对象；
   * **本地方法栈JNI**（即一般说的 **Native** 方法）引用的对象。

## 垃圾回收算法

#### **复制算法**

  将内存分为大小相等的两块，每次程序只使用其中一块，当GC发生的时候，把存活的对象复制到另外一块内存中，整齐的排列，然后清空原来的那块内存。

  缺点：

   * 每次只能使用一半的内存，造成空间的浪费。
   * 当存活对象较多时，复制的性能较差。

#### **标记-清除算法（Mark-Sweep）**

  顾名思义，先标记垃圾，再清除。它是 GC 最基础的算法，后续很多算法都是基于它上面去改进的。标记的过程是通过搜索 GC 对象的算法。被标记的对象，在统一GC的时候会把标记的对象清除掉。但是，**这个算法有一个很明显的缺点，就是在垃圾回收后会产生大量不连续的碎片空间，导致程序要申请较大的对象时常无法找到合适的内存空间，迫使再次GC**。

#### **标记-整理算法（Mark-Compact）**

  **标记-整理** 算法是在 **标记-清除** 算法上的改进，即把清除后存活的对象统一整理到连续的内存中，这样就不会造成内存碎片。

#### **分代收集算法**

  **分代算法并不是一个特定的算法**。而是把内存分成多个区域，一般为新生代、老年代等。然后根据不同区域不同的特点，用不同回收算法去回收垃圾。例如新生代，对象存活率低，比较适用复制算法。老年代存活率高，比较适用 **Mark-Compact** 算法。

## 垃圾回收器

#### 7款经典的垃圾回收器

* 串行回收器：**Serial**、**Serial Old**
* 并行回收器：**ParNew**、**Parallel Scavenge**、**Parallel Old**
* 并发回收器：**CMS**、**G1**



#### 7款经典的垃圾回收器与垃圾分代之间的关系

* 新生代收集器：**Serial**、**ParNew**、**Parallel Scavenge**
* 老年代收集器：**Serial Old**、**Parallel Old**、**CMS**
* 整堆收集器：**G1**

<img width="800px" src="_media/垃圾回收器分代关系.png">

#### 垃圾回收器的组合关系

<img width="800px" src="_media/垃圾回收器组合.png">


* 两个收集器之前有连线，表明他们可以搭配使用： **Serial/Serial Old**、 **Serial/CMS**、**Parnew/Serial Old**、**ParNew/CMS**、**Parallel Scavenge/Serial Old**、**Parallel Scavenge/Parallel Old**、**G1**
* 其中 **Serial Old** 作为 **CMS** 出现 **Concurrent Mode Failure** 后的预案
* JDK 8 时，将 **Serial/CMS** 和 **ParNew/Serial Old** 这个两个组合声明为废弃，并在JDK 9中移除。
* JDK 14 中弃用 **Parallel Scavenge/Serial Old** 组合
* JDK 14 中删除 **CMS**垃圾回收器

#### 总结
<img width="100%" src="_media/GC.png">
