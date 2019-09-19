## hadoop学习（一）----入门

Hadoop是什么

* 是一个由Apache基金会开发的分布式系统基础架构
* 主要解决海量数据的存储和海量数据的分析计算问题
* Hadoop通常指的是Hadoop生态

Hadoop三大发行版本

* Apache(入门),Cloudera（收费的）,Hortonworks（文档好）

Hdoop的优势

* 高可靠性：因为Hadoop假设计算元素和存储会出现故障，因为它维护多个工作数据副本，在出现故障时可以对失败的节点重新分布处理
* 高拓展性，在集群间分配任务数据，可以方便的扩展数以千计的节点
* 高校属性：在MapReduce的思想下Hadoop时并行工作的，以加快任务的处理速度
* 高容错性



Hadoop组成：

![1566184240943](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566184240943.png)

hadoop组成其中hdfs就可以看作一个磁盘，用于存储，Yarn就相当于一个作业，里面有一个任务，比如分析指标。然后MapReduce用于处理任务的计算流程。

![1566184466366](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566184466366.png)



**HDFS架构概述**

* NameNode（nn）:存储文件的元数据，比如文件名，文件目录，文件属性（生成时间，副本数，文件权限等），以及每个文件的块列表和块所在的DataNode等。

  > 大白话说NameNode相当于图书馆中所有书所在的地址目录

* DataNode（dn）：在本地文件系统存储文件块数据，以及块数据的校验和

  > 大白话说DataNode就相当于图书馆中指定一本书的内容

* Secondary NameNode(2nn) ：用来监控HDFS状态的辅助后台程序，每隔一段时间获取HDFS元数据的快照



YARN架构概述

![1566185077602](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566185077602.png)

其中ResourceManager就是所有的集群资源分配的老大，而NodeManager就时单个集群节点上，规划给该集群下的自己群资源分配（cpu，内存，磁盘）的管理者。![1566185365565](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566185365565.png)



**MapReduce架构概要**

![1566185693580](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566185693580.png)

就好像你输入一串数据，我们对其切割提取出其中的各个指标，然后再根据每个指标合并成目标数据。Map就是切割提取操作，而Reduce就对这些切割提取出的结果进行汇总计算，归并到目标结果中。





**大数据的生态体系**

![1566194287991](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566194287991.png)

![1566194714219](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566194714219.png)

