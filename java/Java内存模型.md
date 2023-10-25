## Java内存模型——JMM

1.Java并发采用共享内存模型，线程之间通过读写内存的公共状态进行通讯。多个线程之间不能通过直接传递数据交互的，他们之间交互只能通过共享变量实现

2.主要目的是定义程序中各个变量的访问规则

3.Java内存模型规定所有变量都存储在主内存中，每个线程还有自己的工作内存。
（a）线程的工作内存中保存了被该线程使用到的变量的拷贝（从主内存中拷贝过来），线程对变量的所有操作都必须在工作内存中执行，而不能直接访问主内存中的变量。
（b）不同线程之间无法直接访问对方工作内存的变量，线程间变量值的传递都要通过主内存来完成
（c）主内存主要对应Java堆中实例数据部分。工作内存对应于虚拟机栈中部分区域。

4.Java线程之间的通信由内存模型JMM控制。
（a）JMM决定一个线程对变量的写入何时对另一线程可见。
（b）线程之间共享变量存储在主内存中。
（c）每个线程有一个私有的本地内存，里面存储了读/写共享变量的副本。
（d）JMM通过控制每个线程的本地内存的交互，来为程序员提供内存可见性保证。

5.可见性、有序性
（a）当一个共享变量在多个本地内存中有副本时，如果一个本地内存修改了该变量的副本，其他变量应该能够看到修改后的值，此为可见性。
（b）保证线程的有序执行，这个为有效性（保证线程安全）。

6.内存间交互操作
（a）lock（锁定）：作用于主内存的变量，把一个变量标识为一条线程独占状态。
（b）unlock（解锁）：作用于主内存的变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。
（c）read（读取）：作用于主内存变量，把主内存的一个变量读取到工作内存中。
（d）load（载入）：作用于工作内存，把read操作读取到工作内存的变量载入到工作内存的变量副本中。
（e）use（使用）：作用于工作内存的变量，把工作内存中的变量值传递给一个执行引擎。
（f）assign（赋值）：作用于工作内存的变量，把执行引擎收到的值赋值给工作内存的变量。
（g）store（存储）：把工作内存的变量的值传递给主内存。
（h）write（写入）：把store操作的值写入到主内存中

>  注意：
>
>  1. 不允许read、load、store、write操作之一单独出现
>  2. 不允许一个线程丢弃assign操作
>  3. 不允许一个线程不经过assign操作，就把工作内存中的值同步到主内存中
>  4. 一个新的变量只能在主内存中生成
>  5. 一个变量同一时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程执行多次，只有执行相同次数的unlock操作，变量才会解锁
>  6. 如果对一个变量进行lock操作，将会清空工作内存中次变量的值，在执行引擎使用这个变量前，需要重新执行load或者assign操作初始化变量的值
>  7. 如果一个变量没有被锁定，不允许对其执行unlock操作，也不允许unlock一个被其他线程锁定的变量
>  8. 对一个变量执行unlock操作之前，需要将该变量同步回主内存中





## 内存屏障

作者：hashcon
链接：[https://juejin.cn/post/7080889290669948936](https://juejin.cn/post/7080889290669948936)
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
### 为何需要内存屏蔽
指令乱序主要是 **CPU 乱序（包括 CPU 内存乱序 和 CPU 指令乱序） **以及** 编译器乱序。**
内存屏障可以用于防止这些乱序。如果内存屏障对于编译器和 CPU 都生效，那么一般称为硬件内存屏障，如果只对编译器生效，那么一般称为软件内存屏障。

### CPU 内存乱序相关
#### 简易CPU模型-总结

1. CPU 每次直接访问内存太慢，会让 CPU 一直处于 Stall 等待。为**了减少 CPU Stall，加入了 CPU 缓存**。
2. CPU 缓存带来了多 CPU 之间的缓存不一致，所以**通过 MESI 这种简易的 CPU 缓存一致性协议协调不同 CPU 之间的缓存一致性**。
3. 对于 MESI 协议中的一些机制进行优化，进一步减少 CPU Stall。
4. 通过将更新放入 Store Buffer，让更新发出的 Invalidate 消息不用 CPU Stall 等待 Invalidate Response。
5. Store Buffer 带来了指令（代码）乱序，需要内存屏障指令，强制当前 CPU Stall 等待刷完所有 Store Buffer 中的内容。这个内存屏障指令一般称为写屏障。
6. 为了加快 Store Buffer 刷入缓存，增加 Invalidate Queue。

### CPU 指令乱序相关
指令并行化
#### CPU流水线模式
执行一条指令一般分为如下几个阶段：取指（Instruction Fetch，IF）、译码（Instruction Decode，ID）、执行（Execute，EXE）、存取（Memory，MEM）、写回（Write-Back，WB）。
![](https://cdn.nlark.com/yuque/0/2023/webp/12929397/1697610029799-706db3db-483a-4dce-8cb7-8c56fea65231.webp#averageHue=%23f5ead2&clientId=u653b258a-8910-4&from=paste&id=ue66c1c10&originHeight=303&originWidth=1041&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u466e0efe-d4cf-41fe-ba24-a28e55b0d4f&title=)
如图，CPU 的执行阶段分为：

1. Instruction Fetch：批量拉取一批指令，进行指令分析，分析其中的循环以及依赖，分支预测等等
2. Instruction Decode：指令译码，与前面的流水线模式大同小异
3. Reservation stations：需要操作数输入的指令，如果输入就绪，就进入 Functional Unit（FU）处理，如果没有就绪就监听 Bypass network，数据就绪发挥信号到 Reservation stations，让指令进入 FU 处理。
4. Functional Unit：处理指令
5. Reorder Buffer：会将指令按照原有程序的顺序保存，这些指令在被 dispatched 后添加到列表的一端，而当他们完成执行后，从列表的另一端移除。通过这种方式，指令会按他们 dispatch 的顺序完成。

这样的结构设计下，可以保证写入 Store Buffer 的顺序，与原始指令顺序一样。但是加载数据，以及计算是并行执行的。前面我们已经知道了在我们的简易 CPU 架构里面，有着多 CPU 缓存 MESI，Store Buffer 以及 Invalidate Queue 导致读取不到最新的值，这里的乱序并行加载以及处理更加剧了这一点。并且，结构设计下，仅能保证检测出同一个线程下的指令之间的互相依赖，保证这样的互相依赖之间的指令执行顺序是对的，但是多线程程序之间的指令依赖，CPU 批量取指令以及分支预测是无法感知的。**所以还是会有乱序。这种乱序，同样可以通过前面的内存屏障避免、**

### 实际的 CPU
实际的 CPU 多种多样，有着不同的 CPU 结构设计以及不同的 CPU 缓存一致性协议，就会有**不同种类的乱序**，如果每种单独来看，就太复杂了。所以，大家通过一种标准来抽象描述不同的 CPU 的乱序现象（即第一个操作为 M，第二个操作为 N，这两个操作是否会乱序，是不是很像 Doug Lea 对于 JMM 的描述，其实 Java 内存模型也是参考这个设计的），参考下面这个表格：
![](https://cdn.nlark.com/yuque/0/2023/webp/12929397/1697611939624-168582a9-0d36-4a97-80b2-94e653608669.webp#averageHue=%23f4f3f3&clientId=u653b258a-8910-4&from=paste&id=u3aa66f43&originHeight=1025&originWidth=633&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=ue7d2a494-cf81-4418-a349-004ae20b26d&title=)
我们先来说一下每一列的意思：

1. Loads Reordered After Loads：第一个操作是读取，第二个也是读取，是否会乱序。
2. Loads Reordered After Stores：第一个操作是读取，第二个是写入，是否会乱序。
3. Stores Reordered After Stores：第一个操作是写入，第二个也是写入，是否会乱序。
4. Stores Reordered After Loads：第一个操作是写入，第二个是读取，是否会乱序。
5. Atomic Instructions Reordered With Loads：两个操作是原子操作（一组操作，同时发生，例如同时修改两个字这种指令）与读取，这两个互相是否会乱序。
6. Atomic Instructions Reordered With Stores：两个操作是原子操作（一组操作，同时发生，例如同时修改两个字这种指令）与写入，这两个互相是否会乱序。
7. Dependent Loads Reordered：如果一个读取依赖另一个读取的结果，是否会乱序。
8. Incoherent Instruction Cache/Pipeline：是否会有指令乱序执行。

举一个例子来看即我们自己的 PC 上面常用的 x86 结构，在这种结构下，仅仅会发生 Stores Reordered After Loads 以及 Incoherent Instruction Cache/Pipeline。其实后面要提到的 LoadLoad，LoadStore，StoreLoad，StoreStore 这四个 Java 中的内存屏障，为啥在 x86 的环境下其实只需要实现 StoreLoad，其实就是这个原因。

### 编译器乱序
除了 CPU 乱序以外，在软件层面还有编译器优化重排序导致的，其实编译器优化的一些思路与上面说的 CPU 的指令流水线优化其实有些类似。比如编译器也会分析你的代码，对相互不依赖的语句进行优化。对于相互没有依赖的语句，就可以随意的进行重排了。但是同样的，编译器也是只能从单线程的角度去考虑以及分析，并不知道你程序在多线程环境下的依赖以及联系。
编译器乱序，可以通过增加不同操作系统上的编译器屏障语句进行避免。
同时，我们在实际使用的时候，一般内存屏障指的是硬件内存屏障，即通过硬件 CPU 指令实现的内存屏障，**这种硬件内存屏障一般也会隐式地带上编译器屏障**。编译器屏障一般被称为软件内存屏障，仅仅是控制编译器软件层面的屏障，举一个例子即 C++ 中的 volaile，它与 Java 中的 volatile 不一样， C++ 中的 volatile 仅仅是禁止编译器重排即有编译器屏障，但是无法避免 CPU 乱序。
## 
## 层层递进可见性与 Java 9+ 内存模型的对应 API
链接：[https://juejin.cn/post/7080889478042419213](https://juejin.cn/post/7080889478042419213)
这里主要参考了 Aleksey 大神的思路，去总结出不同层次，层层递进的 Java 中的一些内存可见性限制性质以及对应的 API。Java 9+ 中，将原来的普通变量（非 volatile，final 变量）的普通访问，**定义为了 Plain**。普通访问，没有对这个访问的地址做任何屏障（不同 GC 的那些屏障，比如分代 GC 需要的指针屏障，不是这里要考虑的，那些屏障只是 GC 层面的，对于这里的可见性没啥影响），会有前面提到的各种乱序。那么 Java 9+ 内存模型中究竟提出了那些限制以及对应这些限制的 API 是啥，我们接下层层递进讲述。

### Coherence（相干性，连贯性）与 Opaque
![](https://cdn.nlark.com/yuque/0/2023/webp/12929397/1697617934990-65c65af0-4463-48ab-a1e8-7629f5f89208.webp#averageHue=%23acbbdc&clientId=u653b258a-8910-4&from=paste&id=u7377d0c6&originHeight=255&originWidth=1512&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u3233016b-a06b-49c0-bdf0-b2ebea101eb&title=)
coherence 的定义，我引用下原文：
> The writes to the single memory location appear to be in a total order consistent with program order.

即对单个内存位置的写看上去是按照与程序顺序一致的总顺序进行的。看上去有点难以理解，结合上面的例子，可以这样理解：在全局，x 由 0 变成了 1，那么每个线程中看到的 x 只能从 0 变成 1，而不会可能看到从 1 变成 0。

### Causality（因果性）与 Acquire/Release
![](https://cdn.nlark.com/yuque/0/2023/webp/12929397/1697618202025-40592f02-f960-4982-9c23-48db05da8bf6.webp#averageHue=%23b9c5df&clientId=u653b258a-8910-4&from=paste&id=ucf1b21e0&originHeight=378&originWidth=1512&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u7d851cf4-21f4-41a1-917a-19512317546&title=)
**在 Coherence 的基础上，我们一般在某些场景还会需要 Causality**
一般到这里，大家会接触到两个很常见的词，即 happens-before 以及 synchronized-with order。
Causality（因果性），有的地方也叫做 Casual Consistency（因果一致性），它在不同的语境下有不同的含义，我们这里仅特指：可以定义一系列写入操作，如果读取看到了最后一个写入，那么这个读取之后的所有读取操作，都能看到这个写入以及之前的所有写入操作。这是一种 Partial Order（半顺序），而不是 Total Order（全顺序），关于这个定义将在后面的章节详细说明。
在 Java 中，Plain 访问与 Opaque 访问都不能保证 Causality，因为 Plain 没有任何的内存屏障，Opaque 只是有编译器屏障。

### Consensus（共识性）与 Volatile
![](https://cdn.nlark.com/yuque/0/2023/webp/12929397/1697618864341-10fe7ab9-7538-4df5-9e47-70c361aad258.webp#averageHue=%23cfd5e7&clientId=u653b258a-8910-4&from=paste&id=u4c4a6d03&originHeight=774&originWidth=1512&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=uf2f2e438-c5f3-4bc7-ad98-c22fe6a7028&title=)
最后终于来到我们所熟悉的 Volatile 了，Volatile 其实就是在 Release/Acquire 的基础上，进一步保证了 Consensus；Consensus 即所有线程看到的内存更新顺序是一致的，即所有线程看到的内存顺序全局一致。
……
所以，可以得出结论，这个 StoreLoad 是加在 Volatile 写之后的，在后面的 JVM 底层源码分析我们也能看出来。

### Final 的作用
![](https://cdn.nlark.com/yuque/0/2023/webp/12929397/1697620850738-1fd8b32c-9bb1-4595-8721-0832f77f4e3f.webp#averageHue=%23ced4e7&clientId=u653b258a-8910-4&from=paste&id=u651f0795&originHeight=934&originWidth=1512&originalType=url&ratio=2&rotation=0&showTitle=false&status=done&style=none&taskId=u4c8868ad-bc7a-4074-af0f-02bedb10b29&title=)
