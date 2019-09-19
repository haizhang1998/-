### Redis学习（四） ---- Redis发布订阅

一、发布订阅功能

1.简介

​      Redis发布订阅（pub/sub） 是一种消息通信模式，发送者（pub）发送消息，订阅者（sub）接受消息。redis客户端可以订阅任意数量的频道，而当用户订阅了频道，频道要是有新消息，就会推送给订阅者。

2.命令

​       2.1 订阅频道

​              **subscribe chanel [chanel ...]**                          订阅给定的一个或多个频道的信息

​              **psubscribe pattern [pattern ...]**                     订阅一个或多个符合给定模式的频道

​       2.2 发布频道

​               **publish channel message**                               将信息发送到指定的频道

​        2.3 退订频道

​               **unsubscribe [channel [channel ...]]**                 指退订给定的频道

​               **punsubscribe [pattern [pattern ...]]**                   推定所有给定模式的频道

3.实战场景

​                这一功能最明显的用法就是构建实时消息系统，比如普通的即时聊天，群聊等功能。

​                在一个博客网，若有100个粉丝订阅了你，当你发布新文章时，就可以推送消息给粉丝们。

​                微信公众号模式



**二、Redis多数据库**

​       Redis下，数据库是有一个证书索引标志，而不是有一个数据库名称，默认情况下，一个客户端连接到数据库0.redis配置文件中下面的参数来控制数据库的总数：

​                database 16     (从0开始 1 2 3 .。。。。 15)     redis默认使用下标为0的数据库！

​        select  数据库                                 //可以切换数据库  例如切换到下标为1的数据库：select 1

​        move key  db名称                           //将key移到指定db下

​        flushdb                                            //清楚当前的数据库的所有key

​        flushall                                             //清楚整个redis数据库的所有key