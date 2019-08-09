# HBase Shell命令常用

# 1. 进入与退出hbase shell命令行窗口

```javascript
[root@repo ~]# hbase shell
hbase(main):001:0>

hbase(main):001:0> exit
[root@repo ~]#
```

# 2. help

## (1) 查看hbase中有哪些命令

![img](https://ask.qcloudimg.com/http-save/yehe-3170721/px85n846dk.png?imageView2/2/w/1620)

## (2) 查看某一组命令的下的所有命令的简介和简单示范

```javascript
# 例如
help 'ddl'
```

## (3) 查看某个命令的详细使用

```javascript
# 例如
help 'create'
```

# 3. general组中命令

```javascript
--(1) 查看集群状态
hbase(main):078:0> status
1 active master, 0 backup masters, 1 servers, 0 dead, 3.0000 average load

--(2) 查看集群版本
hbase(main):079:0> version
1.2.6, rUnknown, Mon May 29 02:25:32 CDT 2017

--(3) 当前登录用户角色信息
hbase(main):080:0> whoami
root (auth:SIMPLE)
    groups: root

--(4) 查看对某张表进行操作的基本命令
hbase(main):081:0> table_help
```

# 4.ddl组中命令

## (1) 查看数据库中有哪些表

```javascript
hbase(main):005:0> list
TABLE
person
```

## (2) 查看表属性信息

```javascript
hbase(main):007:0> describe 'person'
# 或者
hbase(main):007:0> desc 'person'

Table person is ENABLED
person
COLUMN FAMILIES DESCRIPTION
{
NAME => 'age', 
BLOOMFILTER => 'ROW', 
VERSIONS => '1', 
IN_MEMORY => 'false', 
KEEP_DELETED_CELLS => 'FALSE', 
DATA_BLOCK_ENCODING => 'NONE', 
TTL => 'FOREVER', 
COMPRESSION => 'NONE', 
MIN_VERSIONS => '0', 
BLOCKCACHE => 'true', 
BLOCKSIZE => '65536', 
REPLICATION_SCOPE => '0'
}                                                     

# 其中的属性的意义：
NAME：列族名
VERSIONS：最大版本号
MIN_VERSIONS：最小版本号
TTL（Time To Live）：存活时间
IN_MEMORY：是否开启缓存，默认false，应该开启，否则与BLOCKCACHE冲突
BLOCKCACHE：读缓存是否开启，默认开启，64M
```

## (3) 建表

```javascript
# create '表名', '列族1', '列族2'...
hbase(main):002:0> create 'person', 'name', 'age'
# 等价于
hbase(main):002:0> create 'person',{NAME=>'name' },{NAME=>'age'}
# 建表时可以指定表属性信息
hbase(main):005:0> create 'user_info',{NAME=>'base_info',VERSIONS=>3 },{NAME=>'extra_info',IN_MEMORY=>'true'} 
```

## (4) alter 修改表

```javascript
--(1) 增加列族
alter 'table_name', 'add_family'
# 或者
alter 'table_name', {NAME => 'add_family'}
# 当然，新增加的列可以设置属性，比如
alter 'table_name', {NAME => 'add_family', VERSIONS => 3}

--(2) 删除列族
alter 'table_name', {NAME => 'delete_family', METHOD => 'delete'}
或者
alter 'table_name', 'delete' => 'delete_family'

--(3) 添加列族f1同时删除列族f2
alter 'user', {NAME => 'f1'}, {NAME => 'f2', METHOD => 'delete'}

--(4) 修改列族
# 将user表的f1列族版本号改为5
alter 'user', NAME => 'f1', VERSIONS => 5
```

## (5) 判断表是否存在

```javascript
exists 'table_name'
```

## (6) 禁用表与启用表

```javascript
--(1) 禁用表
disable 'table_name'
--(2) 查看表是否禁用
is_disabled 'table_name'
--(3) 启用表
enable 'table_name'
--(4) 查看表是否启用
is enabled 'table_name'
```

## (7) 删除表

```javascript
# hbase的某些版本，在删除表前，需要先禁用表
disable 'table_name'
drop 'table_name'
```

# 5. dml组中命令

## (1) 插入数据

```javascript
--(1) 
# put '表名','rowkey','列族名:列名','值'
put 'person','0001','name:firstname', 'Jed'
--(2) 可以指定时间戳，否则默认为系统当前时间
put 'person','0002','info:age',20,1482077777778
```

## (2) 查询某行

```javascript
--(1) 
get 'person', '0001'
--(2) 查询某行，指定列名
get 'person', '0001', 'name:firstname'
--(3) 查询某行，添加其他限制条件
# 查询person表中，rowkey为'0001'的这一行，只显示name:firstname这一列，并且只显示最新的3个版本
get 'person', '0001', {COLUMNS => 'name:firstname', VERSIONS => 3}
# 查看指定列的内容，并限定显示最新的3个版本和时间范围 
get 'person', '0001', {COLUMN => 'name:first', VERSIONS => 3, TIMERANGE => [1392368783980, 1392380169184]}
# 查询person表中,rowkey为'rk0001',且某列的内容为'中国'的记录
scan'person', 'rk0001', {FILTER => "ValueFilter(=, 'binary:中国')"}
```

## (3) 全表扫描

```javascript
--(1) 扫描全表
scan 'person'
--(2) 扫描时指定列族
scan 'person', {COLUMNS => 'name'}
--(3) 扫描时指定列族，并限定显示最新的5个版本的内容
scan 'person', {COLUMNS => 'name', VERSIONS => 5}
--(4) 设置开启Raw模式，开启Raw模式会把那些已添加删除标记但是未实际删除的数据也显示出来
scan 'person', {COLUMNS => 'name', RAW => true}
--(5) 列的过滤
# 查询user表中列族为info和data的信息
scan 'user', {COLUMNS => ['info', 'data']}
# 查询user表中列族为info，列名为name、列族为data，列名为pic的信息
scan 'user', {COLUMNS => ['info:name', 'data:pic']}
# 查询user表中列族为info，列名为name的信息，并且版本最新的5个
scan 'user', {COLUMNS => 'info:name', VERSIONS => 5}
# 查询user表中列族为info和data且列名含有a字符的信息
scan 'user', {COLUMNS => ['info', 'data'], FILTER => "(QualifierFilter(=,'substring:a'))"}
# 查询user表中列族为info，rk范围是[rk0001, rk0003)的数据
scan 'people', {COLUMNS => 'info', STARTROW => 'rk0001', ENDROW => 'rk0003'}
# 查询user表中row key以rk字符开头的
scan 'user',{FILTER=>"PrefixFilter('rk')"}
# 查询user表中指定时间范围的数据
scan 'user', {TIMERANGE => [1392368783980, 1392380169184]}
```

scan的用法很多，参数，过滤条件可以很多，各种组合， 在此不列举过多的例子，参考 help 'scan'

## (4) 删除数据

```javascript
delete 'table_name', 'rowkey', 'family:column'
```

## (5) 清空表

```javascript
truncate 'table_name'
```

# 6. 其他

```javascript
--(1) 查看命名空间
hbase(main):015:0> list_namespace
NAMESPACE

default # 用户创建的表放在这里
hbase # 系统表空间

--(2) 把表中数据强制写到磁盘
flush 'table_name'

#再hbase shell中无法使用Backspace需要如下操作
.使用Ctrl + Backspace或Shift + Backspace组合键删除
```