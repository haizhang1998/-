### Redis学习（二）---- Redis常用命令和注意事项

###redis常用命令

 key键命令用于管理redis的键

​        **keys *  ** 查询所有的key

​       **set keyName keyValue  **    插入一个键值，键值分别为keyName 和keyValue 

​       **get keyName **     得到对应的keyName的值

​       **del key**   该命令用在可以存在时删除

​       **dump key** 序列化给定key 并返回被序列化的值

​       **Exisits key** 查询给定的key是否存在

​      **Expire key seconds**  为给定key设置过期时间（以秒计）

> expire key的应用场景：    * 活动限时时间
>
> ​                                *活动数据缓存（对一些需要定时更新的数据：如积分排行榜）
>
> ​                                \* 手机验证码
>
> ​                                *限制网站方可的访问评论 （例如1分钟最多访问10次）

​                                 

​       **TTL key**  以秒为单位，返回给定key剩余生存时间（time to live），若返回-1 则为永久存储，-2则表示无效（key消失了）

​       **PTTL key** 以毫秒为单位，返回给定key剩余生存时间（time to live）

​       **keys pattern**  查找所有符合给定模式的key （key即是通配符）

​       **rename key newKey** 修改key的名称

> 修改key的名字是要注意，可能数据库中存在同名的（你修改后同名），那你这个key的值会替代那个与你同名的值！ 并且如果与你同名的key设置了expire时间，那这个时间就会会和rename的那个key的失效时间一致！（如果那个key没有失效时间，将会是永久存储！）

​       ![1567753871120](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1567753871120.png)

**type key**     返回Key类型



**和清除数据库中数据相关的指令**

​	  **flushdb**        命令清除数据，只会清除当前的数据库下的数据，不会影响到其他数据库。

​      **flushall**         命令会清除这个实例的数据。在执行这个命令前要格外小心。 （会将所有数据库的数据干掉）



###Redis切换数据库

redis下，数据库是由一个整数索引标识，而不是由一个数据库名称。默认情况下，一个客户端连接到数据库0。redis配置文件中下面的参数来控制数据库总数：

/etc/redis/redis.conf
文件中，有个配置项 databases = 16 （默认有16个数据库）

![1567752837430](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1567752837430.png)

可以通过下面的命令来切换到不同的数据库下
`select 2`

意思是从当前的数据库切换到2号数据库中（也就是db2）

**将当前数据库中的key迁移到指定的数据库中去**

**move key dbname**  将当前数据库的key移动到给定dbname的数据库

```shell
#代表将key1 从当前数据库迁移到2号数据库中
move key1  2 
```

> 每个数据库都有属于自己的空间，不必担心之间的key冲突。



###key的命名规范

1. key不能太长，不要超过1024字节，这不仅消耗内存而且会降低查找的效率

2. key也不要太短，太短的话，Key的可读性会下降

3.  key的名称严格区分大小写        

4. 在一个项目中，key最好使用统一的命名模式 ，例如user:123:password;

​                         原因： 一般数据库和javabean都有个User类和表。而User字段包含id/name/age/uname

​                                      为什么是：而不是_做分隔符，因为怕表中字段含有下划线命名的字段名。这是行业规范

