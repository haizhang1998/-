### Redis学习（三） ---- Redis数据结构

Redis支持多种数据结构：

主要有：字符串，hash，列表，集合，有序集合。

![1567737166245](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1567737166245.png)



### 一、字符串String

![1567737426021](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1567737426021.png)

![1567737548217](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1567737548217.png)

**String类型的常用命令**                

**setnx key value** （分布式锁问题常问） 当key不存在的时候 才给key赋值创建key，存在的话就无效。

**getrange key start end**   获取存储在指定key中字符串的子字符串 范围在start 和end间

> getrange key 0  -1   获取全部长度的key值
>
> getrange key -1 0    空字符串！
>
> getrange key  1  3   获取key的第2个到第3个字符的组合

**getset keyname newValue **  获取keyname对应值并重新设置新值给keyname

**strlen key**         返回key所有储存的字符串值的长度

**append key_name value**   将拼接的字符串接到指定key-name的后面

![1567755096127](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1567755096127.png)

String应用场景：

* 保存单个字符串或json字符串数据

* 因String为二进制安全 ，所以你完全可以将图片文件的内容做字符串存储

* 计数器 (常规key-value 缓存使用，常规技术：粉丝数等)

​      INCR等指令都是本身具有原子性，也就是并发操作时，如果时同时点击，必须排队处理。保证线程的安全

**自增自减**

​                    incr keyname :    Incr命令将key中存贮的数字值加1，如果key不存在，则key的值会先被初始化为0

​                                                   然后再执行INCR操作

​                    自减：DECR key_name  或者  DECYBY key_name 减少的值 

​                     自增： incr key_name 或者 incrby key_name 增加的值

**使用字符串类型须知：**

string是redis最基本类型，一个键最大存储512MB.它是二进制安全的，也就是说string在传输中不会发生安全的问题

​       二进制安全指的是，在传输数据时，保证二进制数据的信息安全，也就是不被篡改破译等。

​       二进制安全特点：* 编码、解码发生在客户端，执行效率高

​                                     \* 不需要频繁的编解码，否则可能出现乱码问题



###二、哈希Hash

**2.1 简介**

​       Redis hash 是一个string类型的field 和 value 的映射表，hash特别适合用于存储对象,Redis中每一个hash可以存储2的32次方-1个键值对（40多亿） 可以看成具有Key 和value的map容器，该类型非常适合于存储值对象的信息，如 uname ,upass,age等。该类型的数据仅占用很少的磁盘空间(相比于json)

**2.2 Hash命令**

​      Hash适合存储对象（javabean）

* hash的赋值：**hset key field value **         (为指定的key，设定fild/value,这里field 代表javabean对应属性 而value是属性值)

​                              **hmset key field value [field1,value1]  ..  **   (同时将多个 field-value 域-值)设置到hash表的key中)

* hash的取值:   **hget key filed**            获取指定hash key中对应field 的值

​                               **hgetall key**                获取所有hash表中key存储的键值

​                               **hkeys key**                  获取所有hash表中的字段

​                                **hLen key**                   获取hash表中字段的数量

* 删除语法：**hdel key field [field1]**       删除一个或多个hash表中的字段

* 其他语法:  **hsetnx key field value**     设置一个hash表中的表名key以及对应field的value，如果不存在则设置这三者，若存在对应的key field value 则不起作用

​                          **hincrby key filed increment**  为哈希表key中的指定字段的证书值加上增量increment

​                          **hincrbyFloat key filed increment**  为哈希表key中的指定字段的证书值加上增量increment（浮点）

​                         **hexists key field**         是否存在该域 ，存在1 不存在0





### 三、List

1.赋值语法

​          **LPUSH key value1 [value2]   ...            //将一个或多个值插入到列表头部（从左侧添加）**

​          **RPUSH key value1 [value2] ....            //在列表中添加一个或多个值(从右侧添加)**

​          **LPUSHX key value                                //将一个值插入到已存在的列表头部（最左侧），如果列表不存在，操作则无效**

​          **RPUSHX key value                               //一个值插入已存在的列表尾部 （最右侧），如果列表不存在，操作则无效**

2.取值语法：

​          LLEN key                                              //取列表的长度

​          **LINDEX key index                                //通过索引获取列表元素**

​          **LRANGE key start stop                        //获取表指定范围内的元素(0是第一个 ,-1倒数第一 ，-2倒数第二...)**

3.删除语法

​         Lpop key                   //移除并获取列表的第一个元素（最左侧）

​         RPOP key                                              //移除并获取列表的最后个元素（最右侧）

​         **BLPOP key1 [key2] timeout              //移除并获取列表的第一个元素，如果列表没有元素会阻塞列表直到等待超时或者发现可弹出元素为止。**                                                                                                           

​        **BRPOP key1 [key2] timeout              //移除并获取列表的最后一个元素，如果列表没有元素会阻塞列表直到等待超时或者发现可弹出元素为止。**

> 你可以启动两个客户端， select 到同一个数据库，然后让一个客户端进行阻塞获取，等待一定的时间，另外一个客户端进行lpush操作，看看是否可以达到上面所说的效果



​        **Ltrim key start stop                          //修剪，范围在[start,stop]之内的元素可以保留，否则删除**

4.修改语法

​          **LSET key index value**                       //通过索引设置列表元素的值

​          LINSERT key before|after world value      //在列表的元素前或后插入元素。将值value插入到key当中，位于值world之前或者之后

> Linsert 语法注意 ，当使用before 将value插入到数组指定world的前面时，如果有多个重复的world都匹配，那就会插入到最左侧的world前面，after也是，会插入到最左侧world的后面（插入到第一个匹配元素的左右）   比如链表key为q,值为： 1  2   2    2   3        现在 调用： linsert q  before  2 111
>
> 得到的q结果是:  1 111 2   2   2   3

​    **rpoplpush origin destination         从origin的list中的最右侧的元素弹出一个，将其追加到destination的list中的第一个（最左侧）**



###四、 Set （不确保顺序，元素不可重复） 

类似于java中的hashtable 原因是redis的set数据结构底层使用了inset和hashtable两种数据结构存储的，inset我们可以理解为数组，hashtable即普通的哈希表（key为set的值，value为null）。

inset内部其实是一个数组(int8_t coentents[]数组)，存储数据的时候是有序的，因为再查找数据的时候是通过二分查找实现的。



**1.赋值操作**

**sadd key value1 [value2] ...**                   像指定key的set集合添加一个或多个元素



2.取值方法

**scard  key**                                                  获取集合成员的数目

**smembers key **                                        返回集合中所有的成员

**sismember key member**                       查询member是否是key这个set里面的成员

**srandmember key [count] **                   返回集合中一个或多个随机元素（可以用于抽奖），并不会删除返回的元素



3.删除语法

**srem key member1 [member2] ....**                删除集合中1个到多个member，成功返回1 ，失败返回0

**spop key**                                                            删除并返回set中一个**随机**元素

**smove source destination member**               将member从source中移除到destination集合



4.差集的运算

**sdiff key1 [key2]**                                  返回给定所有集合的差集 （给出key1有的key2没有的内容）

**sdiffstore destination set1 [set2] **    返回给定所有集合的差集 （给出set1有的set2没有的内容）然后将差集的内容存入到destination中



5.交集的语法

**sinter key1 [key2]**                               返回给定所有集合的交集(共有的数据)

**sinterstore destination key1 [key2]**  返回给定所有集合的交集(共有的数据)存进destination中,如果destination中有内容，将会覆盖原先内容

6.并集的语法

**sunion key1 [key2]**                                返回所有给定集合的并集

**sunionstore destination key1 [key2]**   返回所有给定集合的并集 存到destination中。

**上面的并集交集差集可以实现共同关注，共同喜好等，可以统计访问网站的独立ip等。。**



### 五、Zset  （sorted set） 有序集合，不重复

1.简介：

* redis有序集合和集合一样也是string类型元素的集合，且不允许重复成员存在
* **不同的是每个元素都会关联一个double类型的分数，redis正式通过分数来为集合中的成员进行大小排序的**

* **有序集合的成员是唯一的，但是分数（score）可以重复**

* **集合是通过hash表实现的，所以添加删除查找的复杂度都是o(1)。集合中最大的成员数有40多亿成员**

2.命令

   2.1 赋值语法：

​    **zadd key score1 member1 [score2 member2]**                    像有序集合添加一个或多个成员，或者更新已存在的成员分数

​    2.2取值语法

​      **zcard key**                                        获取成员数

​      **zcount key min max**                     计算在有序集合中指定区间分数的成员数

​      **zrank key member**                        返回有序集合中指定成员的索引（排名由低到高，分数越低索引越小！）

​      **zrange key start stop  [withscores]**       通过索引区间返回有序集合指定区间内的成员（评分由低到高）

​      **zrevrange key start stop [withscores]**    返回有序集合中指定区间的成员，通过索引，分数由高到底

   2.3 删除语法

​      **del    key**                                        移除集合

​      **zrem key member [member ...]**       移除有序集合作用一个或者多个成员

​      **zremrangebyrank   key start stop**       移除有序集合中给定的排名区间的所有成员 （第一名是0，默认是排名由低到高）

​      **zramrangebyscore  key min max**       移除有序集合中给定的分数区间的所有成员

3.应用场景

​     常用于制作排行榜，我们可以以发表时间作为score来存储，这样获取时就是自动按照时间排好序的

