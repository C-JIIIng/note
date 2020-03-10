# `HDFS`

## 1. 介绍

+ Hadoop分布式文件系统

**优点**

+ 高容错、高可用、高扩展
  + 数据冗余，**多block多副本**，副本丢失后自动恢复
  + NameNode **HA**、 安全模式
  + 10K节点规模
+ 海量数据存储
+ 构建成本低，安全可靠
+ 适合大规模离线批处理

**缺点**

+ 不适合低延迟数据访问
+ **不适合大量小文件存储**（面试题）
  + 元数据占用NameNode大量内存空间
    + 每个文件、目录和block的元数据都要占用150Byte
    + 存储1亿个元素，大约需要20G内存
    + 如果一个文件为10kb，1亿文件大小进位1Tb，却要消耗掉20GB内存
  + 磁盘寻道时间超过读取时间
+ 不支持并发写入
  + 一个文件同时只能有一个写入者
+ 不支持文件随机修改
  + 仅支持追加写入

## 2. HDFS原理

### 2.1 系统架构

![image-20200223222921774](D:\Desktop\面试\img\image-20200223222921774.png)

![image-20200223223001692](D:\Desktop\面试\img\image-20200223223001692.png)



#### Active NameNode(AN)

+ 活动Master**管理节点**（集群中唯一）

+ 管理命名空间

+ **管理元数据**：文件的位置、所有者、权限、数据块等

+ **管理Block副本**策略：默认3个副本

+ **处理**客户端**读写**请求，为**DataNode分配**任务

  ![image-20200223224039147](D:\Desktop\面试\img\image-20200223224039147.png)

#### Standby NameNode(SN)

+ 热备Master管理节点
+ Active NameNode宕机后，快速升级为新的Active
+ **同步元数据**，即周期性下载edits编辑日志，生成fsimage镜像检查点文件

#### NameNode 元数据文件

+ **edits（编辑日志文件）**：保存了自最新检查点（CheckPoint）之后的所有文件更新操作
+ **fsimage（元数据检查点镜像文件**）：保存了文件系统中所有的目录和文件信息，如：某个目录下有哪些子目录和文件，以及文件名、文件副本、文件由哪些Block组成等
+ Active NameNode 内存中有一份最新的元数据（=fsimage+edits）
+ Standby NameNode在检查点定期将内存中的元数据保存到fsimage文件中

#### DataNode

+ Slave工作节点
+ 存储block和数据校验和
+ **执行**客户端发送的读写请求
+ 通过心跳机制定期（默认3秒）向NameNode 汇报运行状态和Block 列表信息
+ 集群启动时，DataNode 向 NameNode 提供Block列表信息

#### Block数据块

+ HDFS 最小存储单元
+ 文件写入HDFS会被切分成若干个Block
+ Block大小固定，**默认是128M**，可自定义
+ **若一个Block 的大小小于设定值，不会占用这个块空间**
+ 默认情况下每个Block**有3个副本**

#### Client

+ 将文件切分为Block
+ 与NameNode交互，回去文件访问计划和相关元数据
+ 与DataNode交互，读取会写入数据
+ 管理HDFS

### 2.2 存储机制

#### 2.2.1 数据文件存储

##### Block存储

+ block是HDFS的最小存储单元
+ **如何设置Block大小**
  + 目标：最小化寻址开销，**降到1%以下**（寻址时间和存储时间的总和）
  + 默认：**128M**
  + **块太小：寻址时间占比过高**
  + **块太大：Map任务数太少，作业执行速度变慢**
+ **Block和元数据分开存储**：block存储于DataNode，元数据存储于NameNode
+ Block**多副本**
  + 以DataNode节点为备份对象
  + 机架感知：将副本存储在不同的机架上，实现数据的高容错
  + 副本均匀分布：提高访问宽度和读取性能，实现负载均衡

##### Block副本放置策略

+ 副本1：放置在Client所在节点
  + 对于远程Client，系统会随机选择节点
+ 副本2：放在不同的机架节点上
+ 副本3：放在与第二个副本同一个机架的不同节点上
+ 副本n：随机选择
+ 节点选择：同等条件下优先选择空闲节点

![image-20200224163854374](D:\Desktop\面试\img\image-20200224163854374.png)

##### Block文件

> **Block和元数据分开存储**：block存储于DataNode，元数据存储于NameNode

![image-20200224165830889](D:\Desktop\面试\img\image-20200224165830889.png)

![image-20200224165847121](D:\Desktop\面试\img\image-20200224165847121.png)

#### 2.2.2 元数据存储

![image-20200224170324806](D:\Desktop\面试\img\image-20200224170324806.png)

![image-20200224170304001](D:\Desktop\面试\img\image-20200224170304001.png)

##### 元数据的两种存储形式

+ 内存元数据（NameNode）
+ 文件元数据（edits + fsimage）

##### edits（编辑日志文件）

+ Client请求变更操作是，操作首先被写入edits，再写入内存
+ edits文件名通过前/后缀记录当前操作的Transaction Id

##### fsimage（元数据镜像检查点文件）

+ 不会为文件系统的每个更新操作进行持久化，因为写fsimage的速度非常慢
+ fsimage文件名会标记对应的Trandaction Id

##### edits 和 fsimage 的合并机制

+ 参见 2.5 QJM方法



#### 2.3 读写操作

##### 写操作

![image-20200224220758517](D:\Desktop\面试\img\image-20200224220758517.png)

+ client与nn建立通讯，请求上传文件
+ nn首先检查文件系统的目录树，是否存在该目录及该文件
+ nn响应client可以上传文件
+ client将文件切分为block
+ 请求上传block
+ nn检测元数据中的dn信息，返回dn列表（block副本的选择策略）
+ client请求3台dn中的一台dn上传数据，dn1与dn2、dn3建立起管道，响应可以上传
+ client以package 64k的形式在管道中不断传输数据到dn1，然后传输搭配dn2、dn3
+ 在管道的反方向上，逐个发送ack，最终一个dn将管道ack发送给client
+ client向nn发送block写入成功信息

##### 读操作

![image-20200224222338182](D:\Desktop\面试\img\image-20200224222338182.png)

+ client 向 nn发起 RPC 请求，来确定请求文件block所在的位置
+ nn检查目录，查询文件的block和副本的位置
+ 按照dn和客户端的距离由近到远的顺序列表返回给客户端
+ 客户端和最近的dn连接，获取数据，如果block出现错误，client会通知nn，同时从下一个拥有该block副本的dn继续读。
+ 当读完列表的block后，若文件读取还没有结束，会继续开始向nn获取下一个block列表
+ read方法是并行读取block信息
+ 将所有的block合并成一个完整的最终文件



#### 2.4 安全模式

##### 什么是安全模式

+ 安全模式是HDFS的一种特殊状态，在这种状态下，HDFS只接受读数据请求，而不接收写入、删除、修改等变更请求
+ 安全模式是HDFS确保Block数据安全的一种保护机制
+ active nn 启动时，HDFS会进入安全模式，dn主动向nn汇报可用block列表等信息，在系统达到安全标准前，HDFS一直处于“只读“

##### 何时离开安全模式

+ block上报率：dn上报的可用block个数/nn元数据记录的block个数
+ 当 block上报率>= 阈值是，HDFS才能离开安全模式，默认阈值是0.999
+ 不建议手动强制退出安全模式

##### 触发安全模式的原因

+ nn重启
+ nn磁盘空间不足
+ block上报率低于阈值
+ dn无法正常启动
+ 日志中出现严重异常
+ 用户操作不当：如强制关机

##### shell-关闭安全模式

`Hadoop dfsadmin -safemode`

#### 2.5 HDFS 高可用 - HA机制 - 主备切换

> 在hadoop2.x之前，nameNode只有一个，存在单点故障问题，在Hadoop2.x引入了HA机制，hadoop2.0的HA 机制有两种：NFS / QJM 

##### 基本原理

HA机制有两个NameNode，一个是activeNN，一个是standbyNN，两者的状态是可以切换的，但是不能同时是active状态。activeNN 对外服务，standbyNN 不对外服务。activeNN 和 standbyNN 之间通过 NFS 或者 JN（QJM方式） 来同步数据

active NN 会把最近的操作记录写到本地的一个edits文件中，并传输到NFS 或 JN 中。standbyNN 定期的检查，从 NFS 或 JN 中把最近 edits文件读过来，然后把 edits文件和fsimage文件合并成一个新的fsimage，合并后会通知activeNN 来获取这个新fsimage。activeNN 收到新的fsimage之后，替换旧的fsimage。

##### QJM方式

+ 部署奇数（2N+1）个 journalNode
+ journalNode 负责存储edits编辑日志
+ 写edits的时候，只要超过半数的journalNode返回成功，就代表本次写入成功
+ 最多可以容忍N个journalNode宕机
+ 基于paxos算法实现

![image-20200224220212391](D:\Desktop\面试\img\image-20200224220212391.png)

##### zookeeper实现active节点选举

![image-20200224225249293](D:\Desktop\面试\img\image-20200224225249293.png)





## 3. 实操

端口：50070



