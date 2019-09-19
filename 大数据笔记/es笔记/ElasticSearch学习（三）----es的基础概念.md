## ElasticSearch学习（三）----es的基础概念

**第一模块：基本概念**

1. 索引的概念**

   含有相同属性的文档集合，相当于mysql中的数据库

2. **文档的概念**

   文档是可以被索引的基本数据单位，相当于数据库中的表的数据

3. **类型的概念**

   索引可以定义一个或多个类型，文档必须属于一个类型，相当于数据库中的表

4. **分片**

​       每个索引都有很多分片，每个分片就是一个Lucene索引

5. **备份**

   拷贝一份分片就完成了分片的备份

   下面这张图中，主节点中存在这多个索引的分片，方便于检索，缓解客户访问的压力。便于用户去垂直拓展和水平扩展。备份是可以动态修改的，而分片不可以动态修改！

![1565344288679](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565344288679.png)

**第二模块：基本操作**（使用postman）基于RestFul风格

1. **创建索引**

> 注意：索引名必须是小写英文

假设我们现在的es服务开的端口是9201，则创建索引并定义其内部映射规则的方法是“

http://127.0.0.1/9201/index_name

上面是url，index_name就是你想要创建索引的名字

在Put请求body中添加这个索引的映射规则（指定它的mappings内容，比如类型，

字段的属性properties），以下提供两种办法创建索引及其内部定义的mappings信息。

* 创建索引同时定义其内部mappings信息的使用方法

![1565573228304](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565573228304.png)

在创建索引时同时指定mappings映射信息，最外层就是定义mappings，”aritcle“意为类型，也就相当于一个表，”properties“囊括了这个表内部的诸多字段。字段又可以指定其**类型、是否被索引、分词器（analyzer只对text文本内容起作用），store是否独立存储（减少io）**等，请求的时候遵循restful风格，**Put请求就是增添数据，而Post就是更新数据，Delete删除，Get查询**

* 创建索引先，然后再指定mappings

![1565574250842](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565574250842.png)

上面的blog已经被创建好了的索引，如果我们还想添加其内部的mappings信息，就可以进行PUT操作，url拼接上索引名/类型名/在哪出添加定义信息（如_mappings字段）。同样你要检查是否增加成功，到es-head插件那里查看索引信息

![1565574392505](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565574392505.png)

看到下图这样就已经添加成功了！store只有在true的时候才会显示在上面喔

2. **删除索引库**

删除用DELETE请求，直接指定删除的索引名字即可。也可以在head插件内部删除。

![1565574891086](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565574891086.png)

3. **向指定的索引中的类型的字段添加数据**

![1565580641813](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565580641813.png)

只需要在url选择POST请求，并填入索引名/类型/文档id（可选），然后在body中指定字段和值即可

我们在head插件处就可以查询到结果

![1565581055885](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565581055885.png)

4. **文档的删除**

我们只需要指定 索引名/类型/**文档id**（_id） 即可删除文档

![1565585725890](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565585725890.png)

5. **文档的修改**

更新的原理就是先删除再添加

其实和添加的步骤一模一样，只需要指定索引名/类型/**文档id**（_id），并且指定修改的文档字段的值即可修改，注意，**文档id一定要正确**

![1565586297084](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565586297084.png)

想要修改的文档id（_id）是1，然后修改的内容就像添加一般，**字段:值** ，不过这里要注意，如果要进行修改，修改哪个调整哪个字段的值，就算不修改其中的字段，也要讲原来的值加上，比如上面我修改了author_id这个值，name我不想修改，原先的值就是haizhang，故此我还是将其保留不动即可。

**6.查询文档数据**

1. 根据id进行查询

   ![1565598304679](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565598304679.png)

   根据文档id查询的结果。



2. 根据关键字查询指定类型的指定字段

![1565597941991](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565597941991.png)

```json
//返回的结果
{
    "took": 3,                      //耗时多少
    "timed_out": false,        		//是否超时
    "_shards": {	
        "total": 5,
        "successful": 5,
        "skipped": 0,
        "failed": 0
    },
    "hits": {				//命中信息
        "total": 2,  				//匹配到的总数
        "max_score": 0.7590336,		
        "hits": [					//命中的诸多选项
            {				
                "_index": "blog",			//所在索引
                "_type": "author",			//所在的类型
                "_id": "4",					//所在的文档的id
                "_score": 0.7590336,		//得分		
                "_source": {				//文档存放的原始信息
                    "author_id": "004",		
                    "name": "李晓强"
                }
            },
            {
                "_index": "blog",			
                "_type": "author",
                "_id": "5",
                "_score": 0.25316024,
                "_source": {
                    "author_id": "005",
                    "name": "李克强"
                }
            }
        ]
    }
}
```

>注意： 如果字段的内容全部是英文，他会像英文字典一样去查找单词来做匹配，不会划分一个一个英文字符，而是会将英文之间的空格当作分隔符。然后切割成不同块再去匹配。如果name存放的是中文，切记，只能一个中文一个中文的查！！！

3. 根据query_string查询

![1565598896034](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565598896034.png)

>上面查询中，如果你之前定义的analyzer指定为standard（标准分词器）的话，它对中文的划分是一个中文一个字符这样划分的，也就是比如”李克强“ 会划分为三个中文，去依次的匹配。命中文档，

4. **查询ik分词器作用域**

ik分词器提供了两种算法 : **ik_smart , ik_max_word** ,其中ik_smart 是最小分割，分割的结果较少。而ik_max_word是最细粒度分割，结果较多。

* ik_smart效果

```json
GET _analyze?pretty
{
  "analyzer": "ik_smart",
  "text": "中华人民共和国国歌"
}

//结果
{
  "tokens": [
    {
      "token": "中华人民共和国",
      "start_offset": 0,
      "end_offset": 7,
      "type": "CN_WORD",
      "position": 0
    },
    {
      "token": "国歌",
      "start_offset": 7,
      "end_offset": 9,
      "type": "CN_WORD",
      "position": 1
    }
  ]
}
```

* ik_max_word效果

```json
GET _analyze?pretty
{
  "analyzer": "ik_max_word",
  "text": "中华人民共和国国歌"
}

//结果
{
  "tokens": [
    {
      "token": "中华人民共和国",
      "start_offset": 0,
      "end_offset": 7,
      "type": "CN_WORD",
      "position": 0
    },
    {
      "token": "中华人民",
      "start_offset": 0,
      "end_offset": 4,
      "type": "CN_WORD",
      "position": 1
    },
    {
      "token": "中华",
      "start_offset": 0,
      "end_offset": 2,
      "type": "CN_WORD",
      "position": 2
    },
    {
      "token": "华人",
      "start_offset": 1,
      "end_offset": 3,
      "type": "CN_WORD",
      "position": 3
    },
    {
      "token": "人民共和国",
      "start_offset": 2,
      "end_offset": 7,
      "type": "CN_WORD",
      "position": 4
    },
    {
      "token": "人民",
      "start_offset": 2,
      "end_offset": 4,
      "type": "CN_WORD",
      "position": 5
    },
    {
      "token": "共和国",
      "start_offset": 4,
      "end_offset": 7,
      "type": "CN_WORD",
      "position": 6
    },
    {
      "token": "共和",
      "start_offset": 4,
      "end_offset": 6,
      "type": "CN_WORD",
      "position": 7
    },
    {
      "token": "国",
      "start_offset": 6,
      "end_offset": 7,
      "type": "CN_CHAR",
      "position": 8
    },
    {
      "token": "国歌",
      "start_offset": 7,
      "end_offset": 9,
      "type": "CN_WORD",
      "position": 9
    }
  ]
}
```

可见，ik_smart用最精简的方式从前往后扫描，组合成一个合理的中文词，而ik_max_word将所有可能成为中文的都进行了一遍拆分

>ik分词器下载地址<a href="https://github.com/medcl/elasticsearch-analysis-ik"></a>
>
>必须下载自己es版本一样的ik分词器，然后将zip包解压再cd到包内进行mvn package打包，在生成订target目录找到zip文件然后放入es下面的plugins目录中，重启es即可

我们在定义字段的时候使用  "analyzer":"ik_smart"   或者  "analyzer":"ik_max_word" 来指定ik分词器的算法。然后查询的时候我们就可以用query_string的方法查询啦。

