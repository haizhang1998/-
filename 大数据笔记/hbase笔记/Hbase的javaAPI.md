#Hbase的javaAPI

# **Java API操作**



​    导入开发包。

​    将hbase安装包中lib下所有jar包导入java项目。



| java类             | HBase数据模型                |
| ------------------ | ---------------------------- |
| HBaseAdmin         | 数据库（DataBase)            |
| HBaseConfiguration |                              |
| HTable             | 表（Table)                   |
| HTableDescriptor   | 列族（Column Family)         |
| Put                | 列修饰符（Column Qualifier） |
| Get                |                              |
| Scanner            |                              |

 



提供了一个接口来管理HBase数据库的表信息。它提供的方法包括：创建表，删除表，列出表项，使表有效或无效，以及添加或删除表列族成员等。

| 返回值                                       | 函数                                                  | 描述                                               |
| -------------------------------------------- | ----------------------------------------------------- | -------------------------------------------------- |
| void                                         | addColumn(String tableName, HColumnDescriptor column) | 向一个已经存在的表添加咧                           |
| checkHBaseAvailable(HBaseConfiguration conf) | 静态函数，查看HBase是否处于运行状态                   |                                                    |
| createTable(HTableDescriptor desc)           | 创建一个表，同步操作                                  |                                                    |
| deleteTable(byte[] tableName)                | 删除一个已经存在的表                                  |                                                    |
| enableTable(byte[] tableName)                | 使表处于有效状态                                      |                                                    |
| disableTable(byte[] tableName)               | 使表处于无效状态                                      |                                                    |
| HTableDescriptor[]                           | listTables()                                          | 列出所有用户控件表项                               |
| void                                         | modifyTable(byte[] tableName, HTableDescriptor htd)   | 修改表的模式，是异步的操作，可能需要花费一定的时间 |
| boolean                                      | tableExists(String tableName)                         | 检查表是否存在                                     |

用法示例：

```
HBaseAdmin admin = new HBaseAdmin(config);
admin.disableTable("tablename")
```



对HBase进行配置。

| 返回值 | 函数                                          | 描述                                                         |
| ------ | --------------------------------------------- | ------------------------------------------------------------ |
| void   | addResource(Path file)                        | 通过给定的路径所指的文件来添加资源                           |
| void   | clear()                                       | 清空所有已设置的属性                                         |
| string | get(String name)                              | 获取属性名对应的值                                           |
| String | getBoolean(String name, boolean defaultValue) | 获取为boolean类型的属性值，如果其属性值类型部位boolean,则返回默认属性值 |
| void   | set(String name, String value)                | 通过属性名来设置值                                           |
| void   | setBoolean(String name, boolean value)        | 设置boolean类型的属性值                                      |

示例：

```
HBaseConfiguration hconfig = new HBaseConfiguration();
hconfig.set("hbase.zookeeper.property.clientPort","2181");
```

​    该方法设置了"hbase.zookeeper.property.clientPort"的端口号为2181。一般情况下，HBaseConfiguration会使用构造函数进行初始化，然后在使用其他方法。



​    可以用来和HBase表直接通信。此方法对于更新操作来说是非线程安全的。

| 返回值           | 函数                                                         | 描述                                             |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| void             | checkAdnPut(byte[] row, byte[] family, byte[] qualifier, byte[] value, Put put | 自动的检查row/family/qualifier是否与给定的值匹配 |
| void             | close()                                                      | 释放所有的资源或挂起内部缓冲区中的更新           |
| Boolean          | exists(Get get)                                              | 检查Get实例所指定的值是否存在于HTable的列中      |
| Result           | get(Get get)                                                 | 获取指定行的某些单元格所对应的值                 |
| byte[][]         | getEndKeys()                                                 | 获取当前一打开的表每个区域的结束键值             |
| ResultScanner    | getScanner(byte[] family)                                    | 获取当前给定列族的scanner实例                    |
| HTableDescriptor | getTableDescriptor()                                         | 获取当前表的HTableDescriptor实例                 |
| byte[]           | getTableName()                                               | 获取表名                                         |
| static boolean   | isTableEnabled(HBaseConfiguration conf, String tableName)    | 检查表是否有效                                   |
| void             | put(Put put)                                                 | 向表中添加值                                     |

用法示例：

```
HTable table = new HTable(conf, Bytes.toBytes(tablename));
ResultScanner scanner =  table.getScanner(family);
```



​    包含了表的名字极其对应表的列族。

| 返回值            | 函数                               | 描述         |
| ----------------- | ---------------------------------- | ------------ |
| void              | addFamily(HColumnDescriptor)       | 添加一个列族 |
| HColumnDescriptor | removeFamily(byte[] column)        | 移除一个列族 |
| byte[]            | getName()                          | 获取表的名字 |
| byte[]            | getValue(byte[] key)               | 获取属性的值 |
| void              | setValue(String key, String value) | 设置属性的值 |

​    用法示例：

```
HTableDescriptor htd = new HTableDescriptor(table);
htd.addFamily(new HcolumnDescriptor("family"));
```



​    维护着关于列族的信息，例如版本号，压缩设置等。它通常在创建表或者为表添加列族的时候使用。列族被创建后不能直接修改，只能通过删除然后重新创建的方式。列族被删除的时候，列族里面的数据也会同时被删除。

| 返回值 | 函数                               | 描述               |
| ------ | ---------------------------------- | ------------------ |
| byte[] | getName()                          | 获取列族的名字     |
| byte[] | getValue(byte[] key)               | 获取对应的属性的值 |
| void   | setValue(String key, String value) | 设置对应属性的值   |

​    用法示例：

```
HTableDescriptor htd = new HTableDescriptor(tablename);
HColumnDescriptor col = new HColumnDescriptor("content:");
htd.addFamily(col);
```



​    用来对单个行执行添加操作。

| 返回值  | 函数                                                        | 描述                                        |
| ------- | ----------------------------------------------------------- | ------------------------------------------- |
| Put     | add(byte[] family, byte[] qualifier, byte[] value)          | 将指定的列和对应的值添加到Put实例中         |
| Put     | add(byte[] family, byte[] qualifier, long ts, byte[] value) | 将指定的列和对应的值及时间戳添加到Put实例中 |
| byte[]  | getRow()                                                    | 获取Put实例的行                             |
| RowLock | getRowLock()                                                | 获取Put实例的行锁                           |
| long    | getTimeStamp()                                              | 获取Put实例的时间戳                         |
| boolean | isEmpty()                                                   | 检查familyMap是否为空                       |
| Put     | setTimeStamp(long timeStamp)                                | 设置Put实例的时间戳                         |

​    用法示例：

```
HTable table = new HTable(conf,Bytes.toBytes(tablename));
Put p = new Put(brow);//为指定行创建一个Put操作
p.add(family,qualifier,value);
table.put(p);
```



​    用来获取单个行的相关信息。

| 返回值 | 函数                                       | 描述                                |
| ------ | ------------------------------------------ | ----------------------------------- |
| Get    | addColumn(byte[] family, byte[] qualifier) | 获取指定列族和列修饰符对应的列      |
| Get    | addFamily(byte[] family)                   | 通过指定的列族获取其对应列的所有列  |
| Get    | setTimeRange(long minStamp,long maxStamp)  | 获取指定取件的列的版本号            |
| Get    | setFilter(Filter filter)                   | 当执行Get操作时设置服务器端的过滤器 |

​    用法示例：

```
HTable table = new HTable(conf, Bytes.toBytes(tablename));
Get g = new Get(Bytes.toBytes(row));
```



​    存储Get或者Scan操作后获取表的单行值。使用此类提供的方法可以直接获取值或者各种Map结构（key-value对）。

| 返回值                      | 函数                                            | 描述                                   |
| --------------------------- | ----------------------------------------------- | -------------------------------------- |
| boolean                     | containsColumn(byte[] family, byte[] qualifier) | 检查指定的列是否存在                   |
| NavigableMap<byte[],byte[]> | getFamilyMap(byte[] family)                     | 获取对应列族所包含的修饰符与值的键值对 |
| byte[]                      | getValue(byte[] family, byte[] qualifier)       | 获取对应列的最新值                     |



​    客户端获取值的接口。

| 返回值 | 函数    | 描述                            |
| ------ | ------- | ------------------------------- |
| void   | close() | 关闭scanner并释放分配给它的资源 |
| Result | next()  | 获取下一行的值                  |



#### 1>**创建表**

```
	@Test
	public void create() throws Exception {
		//创建配置文件
		Configuration configuration=HBaseConfiguration.create();
		//设置配置文件信息
		configuration.set("hbase.zookeeper.quorum", "yun01:2181,yun02:2181,yun03:2181");
		//创建数据库对象
		HBaseAdmin admin=new HBaseAdmin(configuration);
		//创建表对象
		HTableDescriptor hd=new HTableDescriptor(TableName.valueOf("tab02".getBytes()));
		//创建列族对象
		HColumnDescriptor hc1=new HColumnDescriptor("cf01".getBytes());
		//设置列族保存最大历史版本
		hc1.setMaxVersions(3);
		HColumnDescriptor hc2=new HColumnDescriptor("cf02".getBytes());
		hc2.setMaxVersions(3);
		hd.addFamily(hc1);
		hd.addFamily(hc2);
		//创建表
		admin.createTable(hd);
		//关闭连接
		admin.close();
	}
```



```
	@Test
	public void Insert() throws Exception {
		// 创建配置文件
		Configuration conf = HBaseConfiguration.create();
		// 设置配置文件信息
		conf.set("hbase.zookeeper.quorum", "yun01:2181,yun02:2181,yun03:2181");
		// 创建表对象
		HTable table = new HTable(conf, "tab02".getBytes());
		// 创建添加数据的行键对象
		Put put = new Put("row01".getBytes());
		// 设置行键数据
		put.add("cf01".getBytes(), "c01".getBytes(), "wz001".getBytes());
		put.add("cf02".getBytes(), "c01".getBytes(), "wz0010".getBytes());
		put.add("cf01".getBytes(), "c02".getBytes(), "wz010".getBytes());
		// 添加数据
		table.put(put);
		// 关闭连接
		table.close();
	}
```

​    javaapi操作hbase时，入口类为HTable，此对象创建时需要扫描.META表，以及其他操作，这非常耗时，所以，应该将该对象设置为单例，复用该对象，如果需要多个HTable对象，应该使用HTablePool，通过对象池复用对象。

```
HTablePool pool = new HTablePool(conf,10);//不知道为什么过时了？
```

​    hbase所有修改数据的操作都保证了行级别的原子性，

**试验**

​    一次插入100万条数据。

```
	@Test
	public void insertMach() throws Exception{
		Configuration conf=HBaseConfiguration.create();
		conf.set("hbase.zookeeper.quorum", "yun01:2181,yun03:2181,yun02:2181,");
		HTable table = new HTable(conf, "tab02".getBytes());
		List<Put> pList=new ArrayList<Put>();
		for (int i = 1; i <= 1000000; i++) {
			Put put = new Put(Bytes.toBytes("row"+i));
			put.add("cf01".getBytes(), Bytes.toBytes("c"), Bytes.toBytes("val"+i));
			pList.add(put);
			if(i%10000==0) {
				table.put(pList);
				pList=new ArrayList<Put>();
			}
		}
		table.put(pList);
		table.close();
	}
```

​    HBase遍历查询百万数据的时间如下：

![img](https://static.oschina.net/uploads/space/2018/0429/133701_qBEr_3754001.png)

​    大概时间为3-4分钟。



```
	@Test
	public void select() throws Exception {
		// 创建配置文件对象
		Configuration conf = HBaseConfiguration.create();
		// 设置配置文件参数
		conf.set("hbase.zookeeper.quorum", "yun01:2181,yun02:2181,yun03:2181,");
		// 创建表对象
		HTable table = new HTable(conf, "tab02".getBytes());
		// 创建获取数据的行键对象
		Get get = new Get("row01".getBytes());
		// 获取整行数据
		Result result = table.get(get);
		// 获取行数据值
		byte[] bs = result.getValue("cf01".getBytes(), "c01".getBytes());
		// 转化数据
		String string = new String(bs);
		System.err.println(string);
		// 关闭连接
		table.close();
	}
```



```
	@Test
	public void resultList() throws Exception{
		// 创建配置文件对象并配置参数
		Configuration conf=HBaseConfiguration.create();
		conf.set("hbase.zookeeper.quorum", "yun01:2181,yun02:2181,yun03:2181");
		// 创建表对象
		HTable table = new HTable(conf, "tab02".getBytes());
		// 创建获取数据的行键对象
		Scan scan = new Scan("row01".getBytes());
		// 获取结果集
		ResultScanner scanner = table.getScanner(scan);
		// 遍历结果集
		Iterator<Result> it = scanner.iterator();
		while(it.hasNext()) {
			Result result = it.next();
			byte[] bs = result.getValue("cf01".getBytes(), "c01".getBytes());
			String str=new String(bs);
			System.err.println(str);
		}
		// 关闭连接
		table.close();
	}
```



```
	@Test
	public void delete() throws Exception{
		//创建配置文件对象并配置参数
		Configuration conf=HBaseConfiguration.create();
		conf.set("hbase.zookeeper.quorum", "yun01:2181,yun02:2181,yun03:2181");
		// 创建表对象
		HTable table = new HTable(conf, "tab02".getBytes());
		// 创建删除对象
		Delete delete = new Delete("row1".getBytes());
		// 删除数据
		table.delete(delete);
		// 关闭连接
		table.close();
	}
```



```
	@Test
	public void deleteTable() throws Exception{
		// 创建配置文件对象并配置参数
		Configuration conf= HBaseConfiguration.create();
		conf.set("hbase.zookeeper.quorum", "yun01:2181,yun02:2181,yun03:2181");
		// 创建数据库对象
		HBaseAdmin admin = new HBaseAdmin(conf);
		// 禁用表
		admin.disableTable("tab01".getBytes());
		// 删除表
		admin.deleteTable("tab01".getBytes());
		// 关闭连接
		admin.close();
	}
```



### 1．**实现范围查询**

​    如果只设置scan但是不做任何限制，则查询所有数据。

```
Scan scan = new Scan();
```

​    如果设置scan并且设置scan的扫描开始和结束为止则查询范围数据，注意含头不含尾。

```
Scan scan = new Scan();
scan.setStartRow("rk2".getBytes());
scan.setStopRow("rk4".getBytes());
```



​    在scan上提供了方法来实现过滤查询。

```
Scan scan = new Scan();
Filter filter = ...
scan.setFilter(filter)
```



HBase为筛选数据提供了一组过滤器，通过这个过滤器可以在HBase中的数据的多个维度（行，列，数据版本）上进行对数据的筛选操作，也就是说过滤器最终能够筛选的数据能够细化到具体的一个存储单元格上（由行键，列明，时间戳定位）。



```java
Filter filter = new RowFilter(CompareOp.GREATER_OR_EQUAL,new BinaryComparator("rk3".getBytes()));

//--RowFilter配合正则过滤器 可以通过正则表达式从hbase表中筛选所有行键符合正则的数据

!!Filter filter = new RowFilter(CompareOp.EQUAL,new RegexStringComparator("^[^x]*x[^x]*$"));

Filter filter = new PrefixFilter("rkx".getBytes());

Filter filter = new KeyOnlyFilter();

Filter filter = new RandomRowFilter(0.2f);

Filter filter = new InclusiveStopFilter("rk4".getBytes());

Filter filter = new FirstKeyOnlyFilter();

//--ColumnPrefixFilter可以实现按照列的前缀过滤数据

!!Filter filter = new ColumnPrefixFilter("c2".getBytes());

//--ValueFilter可以按照值来过滤数据

!!Filter filter = new ValueFilter(CompareOp.EQUAL,new RegexStringComparator("^[^2]*2.*$"));

//--SingleColumnValueFilter按照某一个指定列的值决定该行是否返回

!!Filter filter = new SingleColumnValueFilter("cf1".getBytes(), "c1".getBytes(), CompareOp.EQUAL, new RegexStringComparator("^[^3]*3.*$"));

//--FilterList 可以将多个过滤器的效果合并起作用

!!Filter f1 = new RowFilter(CompareOp.EQUAL,new RegexStringComparator("^rk\\d+$"));

!!Filter f2 = new KeyOnlyFilter();

!!FilterList fl = new FilterList(Operator.MUST_PASS_ALL, f1,f2);

scan.setFilter(fl);
```