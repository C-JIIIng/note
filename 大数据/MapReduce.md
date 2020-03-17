# MapReduce

> 一种编程模型：MapReduce程序被分为 Map（映射）阶段和Reduce （化简）阶段，用于**大规模数据集的并行计算**，需要将数据分配到大量的机器上计算，每台机器运行一个子计算任务，最后再合并每台机器运算结果并输出

MapReduce将整个并行计算过程抽象到两个函数，在Map中进行数据的读取和预处理，之后将预处理的结果发送到Reduce 中进行合并。

## MR原理

以经典的 WordCount 的例子来说明一下MapReduce的执行流程，WordCount就是统计每个单词出现的次数。

![image-20200226165257153](D:\Desktop\面试\img\image-20200226165257153.png)

+ Job & Task （作业 与 任务）
  + 作业是客户端请求执行的一个工作单元
    + 包括输入数据、 MapReduce 程序、配置信息
  + 任务 是将 作业分解后得到的细分工作单元
    + 分为 Map 任务和 Reduce 任务
+ Split 切片
  + 输入数据被划分成等长的小数据块，成为**输入切片**
  + split是逻辑概念，仅包含元数据信息，如：数据起始位置，长度，所在节点等
  + **每个split 交给一个map任务处理，split的数量决定map任务的数量**
  + split的大小默认等于block大小
  + split的划分方式由程序设定，split与HDFS block没有严格的对应关系
  + split越小，负载越均衡，但集群开销越大，split越大，map任务数少，集群的计算并发度降低。
+ Map阶段（映射）
  + 由若干Map任务组成，**任务数量由split数量决定**
  + 输入：split切片（key-value）输出：中间计算结果：key-value
+ Reduce阶段（化简）
  + 有若干reduce任务组成，**任务数量由程序指定**
  + 输入map阶段的中间结果，输出：最终结果 key-value
+ shuffle
  + map 和 reduce 阶段的中间环节，是虚拟阶段
  + 负责执行 Partition （分区）、 Sort （排序）、 Spill （溢写）、 Merge （合并 ）、 Fetch （抓取）等工作
  + **Partition 决定了map任务输出的每条数据放入哪个分区，交给哪个reduce任务处理**
  + **Reduce 任务的数量决定了 Partition 数量**
  + **Partition 编号 = (Reduce任务编号key_hashcode) % (reduce_task_number)（% 为取模 取余数）**
  + 哈希取模的作用：将 一 个数据集**随机均匀分成若干个子集**
  + 避免和减少 Shuffle 是 MapReduce 程序调优的重点

#### shuffle详解

+ Map端
  + map任务将中间结果写入专用内存缓冲区Buffer（默认100M），同时进行分区和排序
  + 当buffer的数量达到阈值（默认是80%）时，将数据溢写到一个临时文件中，文件内数据先分区再排序
  + map任务结束前，将多个临时文件合并为一个map输出文件，文件内数据先分区后排序
+ Reduce 端
  + reduce 任务从多个map输出文件中主动抓取属于自己的分区数据，先写入buffer中，数量达到阈值后，溢出写到磁盘的一个临时文件中
  + 数据抓取完成后，将多个临时文件合并为一个reduce输入文件，文件内数据按key排序

![image-20200226193617215](D:\Desktop\面试\img\image-20200226193617215.png)

![image-20200226193803327](D:\Desktop\面试\img\image-20200226193803327.png)

![image-20200226194326933](D:\Desktop\面试\img\image-20200226194326933.png)