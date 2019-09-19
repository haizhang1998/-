## ElasticSearch学习（一）----原理及介绍

1. **什么是es？**

   Elasticsearch是面向文档(document oriented)的，这意味着它可以存储整个对象或文档(document)。然而它不仅仅是存储，还会索引(index)每个文档的内容使之可以被搜索。在Elasticsearch中，你可以对文档（而非成行成列的数据）进行索引、搜索、排序、过滤。这种理解数据的方式与以往完全不同，这也是Elasticsearch能够执行复杂的全文搜索的原因之一。

2. **es的应用场景**

   可用应用场景： 海量数据分析引擎

3. **es的安装**

   历史版本：1.x --> 2.x --> 5.x （版本跳跃过大，为了规范版本号直接发行了5.x版本）

   版本的选择: 5.x 

​       直接官网安装即可，安装完成后就进入bin下打开elasticsearch.bat，然后访问对应的端口即可

4. **Elasticsearch Head插件:**
   head插件是将es图形化展现给用户，它是集群管理、数据可视化、增删改查、查询语句可视化工具。
   安装Elasticsearch Head插件前必须安装NodeJs
   一下载Elasticsearch Head插件的文件包
   下载地址:https://github.com/mobz/elasticsearch-head

   下载文件并且解压

   二启动之前 需要安装NodeJs
   nodejs官网:https://nodejs.org/en/
   当前最新版本是10.13.0 本次使用的最新版本

   将下载的文件双击进行安装

   除了修改默认安装路径 其他都使用默认即可
   此次本机安装路径 将head与node存放在同一个文件夹下 便于查找以及管理

   

   三测试环境以及修改默认配置
   1windows+R 输入cmd 在控制台输出node -v查看当前安装node版本

   2配置淘宝镜像
   淘宝镜像官网:http://npm.taobao.org/

   windows+R 输入cmd 打开控制台
   将本地默认的下载镜像的路径修改为淘宝镜像仓库
   输入命令:npm install -g cnpm --registry=https://registry.npm.taobao.org
   由于之前环境已经配置了淘宝镜像 本次不截图
   在使用这个命令可能一次没有成功 需要多输入几次进行安装
   3安装grunt
   grunt作用:启动head插件使用的命令grunt server
   所以必须要使用npm进行安装grunt
   windows+R 输入cmd 在控制台输入
   命令:npm install -g grunt -cli
   提示:如果一次没有安装成功，多次重复当前命令，有时可能由于网络原因导致下载未成功
   4安装cnpm
   打开head插件压缩的文件夹

   在图中圈红地方输入cmd 进入到控制台
   使用命令进行安装cnpm 界面出现All packagesin stalled表示安装成功
   命令:cnpm install

   5修改elasticsearch.yml文件（es文件夹中conf路径下）
   在文件最后加上
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   千万千万注意 http.cors.enabled:与true之间有一个空格 其他也是一样 中间必须有一个空格 如果没有输入空格 这个不会起作用

   cluster.name 集群名字
   node.name: node-01 节点名字
   network.host: 192.168.0.153 一般写成0.0.0.0
   http.port: 9200 访问es默认使用端口
   http.cors.enabled: true ip访问策略 如果使用ip访问不了 一般就是这个配置出现问题
   http.cors.allow-origin: “*”
   6修改Gruntfile.js（在head文件夹下）
   连接集群 两种修改方式 其中9100是访问端口

   

   7修改es地址
   打开elasticsearch-head-master/_site/app.js文件

   将localhost换成安装的ES ip地址 如果是本机 不需要修改 使用默认localhost即可
   四启动ES以及head插件
   1启动ES 找到es文件夹路径 在文件夹路径输入cmd

   然后在控制台输入elasticsearch.bat

   出现文件中标红表示启动成功
   2启动head插件
   在head文件夹下输入cmd

   然后输入grunt server

   启动成功并将地址以及端口暴露出
   浏览器输入127.0.0.1:9100访问head插件 其中127.0.0.1:9200是集群地址 下边.kibana 是由于本机安装kibana插件(不需要关注)

   ![1565341467993](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565341467993.png)