# YARN

## 1. 介绍

在hadoop1.x版本中，MapReduce身兼两职：计算框架+资源管理系统

在hadoop2.x版本中，提出yarn来作为**分布式通用资源管理系统**，从而来**聚焦资源管理**、**通用**（可以适用各种计算框架）、**高可用**、**高扩展**

## 2. YARN原理

### 2.1 系统架构

> 主从结构

![image-20200225080549854](E:\研究生\面试\大数据\img\image-20200225080549854.png)

在整个资源管理框架中， ResourceManager为Master，NodeManager为Slave， ResourceManager 负责对各个NodeManager上的资源进行统一管理和调度。当用户提交一个应用程序时，需要提供一个用以跟踪和管理这个程序的ApplicationMaster，负责向ResourceManager申请资源，并要求NodeManager启动一个可以占用这一定资源的任务。

#### 2.1.1 核心概念

##### ResourceManager（RM）

+ 主要功能
  + **统一管理集群的所有资源**
  + 将**资源按照一定策略分配给各个应用**（ApplicationMaster）
  + 接收**NodeManager的资源上报**信息
+ 有两个组成部分
  + 调度器 scheduler
    + 目的是为了让我们每个节点都充分利用起来，合理分配和调度的一种管理器
    + 值得注意是，调度器是一个纯调度器，**不负责从事任何具体的和应用程序相关的工作，**比如：运行map任务或者reduce任务，监控程序，跟踪程序都不是他的活。
  + 应用程序管理器 Application Manager
    + 负责接收client端传输的job请求，为应用分配一个container（资源池）来运行我们第一个 ApplicationMaster，负责监控ApplicationMaster，并且在遇到失败时重启ApplicationMaster

##### NodeManager（NM）

+ 主要功能
  + 管理**单个节点**的资源
  + 向ResourceManager汇报**节点资源使用情况**
  + 管理**Container**的生命周期
+ 核心组件
  + NodeStatusUpdater
  + ContainerManager
  + ContainerExecutor
  + NodeHealthCheckerService
  + Security
  + WebServer

##### ApplicationMaster（AM）

+ 主要功能
  + **管理应用程序实例**
  + 向ResourceManager（scheduler）**申请**任务执行所需的**资源**
  + **任务（map、reduce）调度和监管**
+ 实现方式
  + **需要为每个应用开发一个AM组件**
  + yarn提供MapReduce的applicationMaster实现
  + 每种组件都是一种事件处理器，在中央事件调度器中注册
  + 采用基于事件驱动的异步编程模型，由中央时间调度器统一管理所有事件

##### Container

+ Container封装了节点上进程的相关资源，**是yarn中资源的抽象**，封装了某个节点的多维资源，如内存，CPU，硬盘，网络，IO等。

+ 分类：
  + 运行applicationMaster的container
  + 运行应用任务的container

#### 2.1.2 工作机制

![image-20200225084409556](E:\研究生\面试\大数据\img\image-20200225084409556.png)

![image-20200226232713300](E:\研究生\面试\大数据\img\image-20200226232713300.png)

## 3. 资源调度器

### 3.1 FIFO调度器

+ 调度策略
  + 将所有的任务放到一个队列，先进队列的线获得资源，排在后面的任务只能等待
+ 缺点
  + 资源利用率低，无法交叉运行任务
  + 灵活性查，如：紧急任务无法插队，耗时长的任务拖慢耗时短的任务

![image-20200226234430061](E:\研究生\面试\大数据\img\image-20200226234430061.png)

### 3.2 容量调度器

+ 核心思想
  + 提前做好预算，在预算指导下分享集群资源
+ 调度策略
  + 集群资源由多个队列分享
  + 每个队列都要**预设资源分配的比例**（提前做预算）
  + 空闲资源优先分配给“实际资源 / 预算资源” 比例最低的队列-最闲的队列（贪心算法）
  + 队列内部采用FIFO调度策略

![image-20200227000942523](E:\研究生\面试\大数据\img\image-20200227000942523.png)

### 3.3 公平调度器

+ 调度策略
  + 多队列公平共享集群资源
  + 通过**平分的方式**，**动态分配资源**，无需预先设定资源分配比例
  + 队列内部可配置调度策略：FIFO、Fair（默认）
+ 资源抢占
  + 终止其他队列的任务，使其让出所占资源，然后将资源分配给占资源量少于最小资源量限定的队列
+ 队列权重
  + 当队列中有任务等待，并且集群中有空闲资源是，每个队列可以根据权重获得不同比例的空闲空间

![image-20200227001047967](E:\研究生\面试\大数据\img\image-20200227001047967.png)