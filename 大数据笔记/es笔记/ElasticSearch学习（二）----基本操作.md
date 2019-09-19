##ElasticSearch学习（二）----搭建集群

1. 在正式开始前，我们来搭建一个集群，设置3个elasticSearch节点，一个是主节点，其余两个为从节点。下面就是步骤

   **step1：复制es目录3份，分别命名为es-cluster-01,es-cluster-02,es-cluster-03**

   ![1565343188951](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565343188951.png)

   **step1:修改复制的elasticSearch中的config目录下elasticsearch.yml文件**

   这里先定义主节点，将es-cluster-01定义为主节点，添加下面的配置

   ```yml
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   #集群名称 必须一致
   cluster.name: my-elasticsearch
   node.master: true
   #节点名称
   node.name: node-1
   #必须为本机的ip地址
   network.host: 127.0.0.1
   #服务端通信端口号，在同一机器侠必须不一样
   http.port: 9201
   #集群间通信端口号，在同一机器下必须不一样
   transport.tcp.port: 9301
   #设置集群自动发现机器ip集合
   discovery.zen.ping.unicast.hosts: ["127.0.0.1:9301","127.0.0.1:9302","127.0.0.1:9303"]
   ```

   其次将es-cluster-02定义为子节点，添加下面的配置

   ```yml
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   #集群名称 必须一致
   cluster.name: my-elasticsearch
   #节点名称
   node.name: node-2
   #必须为本机的ip地址
   network.host: 127.0.0.1
   #服务端通信端口号，在同一机器侠必须不一样
   http.port: 9202
   #集群间通信端口号，在同一机器下必须不一样
   transport.tcp.port: 9302
   #设置集群自动发现机器ip集合
   discovery.zen.ping.unicast.hosts: ["127.0.0.1:9301","127.0.0.1:9302","127.0.0.1:9303"]
   ```

   最后同样的将es-cluster-03也定义为子节点，添加如下配置

   ```yml
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   #集群名称 必须一致
   cluster.name: my-elasticsearch
   #节点名称
   node.name: node-3
   #必须为本机的ip地址
   network.host: 127.0.0.1
   #服务端通信端口号，在同一机器侠必须不一样
   http.port: 9203
   #集群间通信端口号，在同一机器下必须不一样
   transport.tcp.port: 9303
   #设置集群自动发现机器ip集合
   discovery.zen.ping.unicast.hosts: ["127.0.0.1:9301","127.0.0.1:9302","127.0.0.1:9303"]
   ```

   **step2：依次启动主节点，子节点**

   **step3: 启动elasticsearch-head插件，访问地址**

   ![1565343538530](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565343538530.png)

   **step4:填入es的主节点的服务端通信端口号，刷新下插件**

   ![1565343620692](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565343620692.png)

**总结：**

看到上面的结果说明主从集群搭建完毕，你会发现主节点中囊括了所有的数字，而从节点只囊括了一部分，这些数字就代表存储的数据，以防哪一天主节点突然崩溃，所有的数据都失效，那么这个时候剩下的两个子节点依然能够拥有所有的存储的数据，来提供客户端的正常访问。

