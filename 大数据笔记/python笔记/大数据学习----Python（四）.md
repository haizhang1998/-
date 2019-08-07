#大数据学习----Python（四）

#### 字典

字典用于存储“键值对” ，用于快速存取，添加，删除，修改某个值，类似于PHP的联合数组，java的HashMap，JS的json

> 字典是无顺序的数据结构，目的是为了增加数据的访问速度。基于Hash,二叉树或其他类似结构。

**创建字典**

在python中有两个创建字典方法

```python
#第一种方法{"key":"value"}
d = {"name":"haizhang",
     "age" :35,
     "house":["beijing","guangdong"]
    }


#第二种方法:构造器
d = dict([
    ["name","zhangsan"],
    ["age",19]
])
```

**字典的增删改查**

```python
d = {"name":"haizhang",
     "age" :35,
     "house":["beijing","guangdong"]
    }

print(d)
#增加一个height的键，value为185.5
d['height']=185.5
print(d)

#del删除一个键值对
del d['height']
print(d)

#遍历字典的key---value
for key in d:
    print(key)
    print(str(d[key]))

#遍历字典的len
print("dict len :"+str(len(d)))

#顺序对字典比较无干扰，比较字典的时候比较key和value是否一致
d1 = {"name":"haizhang","age":18}
d2 = {"age":18,"name":"haizhang"}
print(d1==d2)


#clear字典
d1.clear()
print(d1)

#values,keys
print(d2.values())
print(d2.keys())
```

结果：

![1565083023643](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565083023643.png)

**字典的内置方法**

![1565082606584](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565082606584.png)

字典获取某个key的值的方法有两种

```python
#使用["key"]得到对象的value
value=d['name']
#使用d.get('name')得到对象的value
d.get('age')
```

![1565083180442](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565083180442.png)

删除字典的某个key-value也有两种方法

```python
#注意del的话是没有返回值的
del d['name']
#pop方法会返回name之前的值
d.pop('name')
```

![1565083455813](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565083455813.png)

**in/not In 判断某个键是否在dict种**

```python
d = {"name":"haizhang",
     "age" :35,
     "house":["beijing","guangdong"]
    }
"name"  in  d           #True
"phone" in  d           #False
"phone" not in d        #True
```





#### 元组

元组的特点是，内部的元素无法改动，它是静态的。

**创建元组**

元组有两种方法创建

```python
#使用（）传入元素
t = (1,2,3)
#使用tuple创建元组
t2 = tuple([1,23,4])
print(t[0],t[1],t[2],t)
```

注意tuple不能增删改，也就是说没有append ，add,remove,pop等操作

#### tuple操作

tuple和list共享所有的操作（除了修改）

```python
t=(1,2,3)

len(t)
max(t)
min(t)
sum(t)
print(t.count(12))
```

tuple也是可以for循环遍历，也可以用in，not in 判断元素是否在元组种。



就像java种的final，一旦确认就不能进行修改了。

