# MapReduce

**MapReduce是一个分布式计算框架，是用户开发“基于Hadoop的数据分析应用”的核心框架。主要由两部分组成：编程模型和运行时环 境。其中，编程模型为用户提供了非常易用的编程接口，用户只需要像编写串行程序 一样实现几个简单的函数即可实现一个分布式程序，而其他比较复杂的工作，如节点 间的通信、节点失效、数据切分等，全部由MapReduce运行时环境完成，用户无须 关心这些细节。**

## 设计思想

**Hadoop MapReduce诞生于搜索领域，主要解决搜索引擎面临的海量数据处理扩展性差的问 题。它的实现很大程度上借鉴了谷歌MapReduce的设计思想，包括简化编程接口、 提高系统容错性等。**

## 优点：

### 		**1.易于编程**：

**传统的分布式程序设计（如MPI）非常复杂，用户需要关注的细节 非常多，比如数据分片、数据传输、节点间通信等，因而设计分布式程序的门槛非常 高。Hadoop的一个重要设计目标便是简化分布式程序设计，将所有并行程序均需要关注的设计细节抽象成公共模块并交由系统实现，而用户只需专注于自己的应用程序逻辑实现，这样简化了分布式程序设计且提高了开发效率。**

###        2.**良好的扩展性**：

**随着公司业务的发展，积累的数据量（如搜索公司的网页量） 会越来越大，当数据量增加到一定程度后，现有的集群可能已经无法满足其计算能力和存储能力，这时候管理员可能期望通过添加机器以达到线性扩展集群能力的目的。**

​     

###        3.**高容错性**：

**在分布式环境下，随着集群规模的增加，集群中的故障率（这里 的“故障”包括磁盘损坏、机器宕机、节点间通信失败等硬件故障和坏数据或者用户 程序bug产生的软件故障）会显著增加，进而导致任务失败和数据丢失的可能性增 加。为此，Hadoop通过计算迁移或者数据迁移等策略提高集群的可用性与容错性。**



###        4.适合PB级以上海量数据的离线处理：

**可以实现上千台服务器集群并发工作，提供数据处理能力。**



## 缺点：

###            1.**不擅长实时计算**：

**MapReduce无法像MySQL一样，在毫秒或者秒级内返回结果。**



###            2.不擅长流式计算

**流式计算的输入数据是动态的，而MapReduce的输入数据集是静态的，不能动态变化。这是因为MapReduce自身的设计特点决定了数据源必须是静态的。**



###             3.**不擅长DAG（有向图）计算**

**多个应用程序存在依赖关系，后一个应用程序的输入为前一个的输出。在这种情况下，MapReduce并不是不能做，而是使用后，每个MapReduce作业的输出结果都会写入到磁盘，会造成大量的磁盘IO，导致性能非常的低下。**



## Hadoop MapReduce核心思想

**从MapReduce自身的命名特点可以看出，MapReduce由两个阶段组成：Map阶段 和Reduce阶段。**

 	- **分布式的运算程序往往需要分成至少2个阶段。**
 	- **第一个阶段的MapTask并发实例，完全并行运行，互不相干。**
 	- **第二个阶段的ReduceTask并发实例互不相干，但是他们的数据依赖于上一个阶段的所有MapTask并发实例的输出。**
 	- **MapReduce编程模型只能包含一个Map阶段和一个Reduce阶段，如果用户的业务逻辑非常复杂，那就只能多个MapReduce程序，串行运行。**



###      MapReduce的运行逻辑：

- **每一个Map阶段和Reduce阶段都可以由多个Map Task和Reduce Task，实际应用中我们只需编写map()和reduce()两个函数，即可完成简单的分布式程序的 设计。**
- **实际应用中我们只需编写map()和reduce()两个函数，即可完成简单的分布式程序的 设计。**
- **map()函数以key/value对作为输入，产生另外一系列key/value对作为中间输出 写入本地磁盘。**
- **MapReduce框架会自动将这些中间数据按照key值进行聚集，且key 值相同（用户可设定聚集策略，默认情况下是对key值进行哈希取模）的数据被统一 交给reduce()函数处理。**
- **reduce()函数以key及对应的value列表作为输入，经合并key相同的value值后， 产生另外一系列key/value对作为最终输出写入HDFS。**