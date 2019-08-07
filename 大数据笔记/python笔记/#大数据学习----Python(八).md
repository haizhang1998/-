#大数据学习----Python(八)

Python的random模块包含用于产生随机数的各种方法，可以通过import进行导入

> 注意：random产生的是伪随机数（就是固定的随机数生成算法，不安全），要产生更加安全的随机数需要使用secrets模块，对于更高要求的随机数需要使用硬件进行生成。

#### random()函数

产生0~1之间的随机数

* 最小0.0
* 最大0.9999......

```python
import random as r
for i in  range(10):
	print(r.random())      #产生10个随机数
```

#### randint(a,b)函数

用于产生随机整数

* 最小a
* 最大b(包含b)

```python
import random as r
#产生5到12之间的随机数，包括5和12
print(r.randint(5,12))
```



### Python文件处理

从文件中读取数据，写入数据

#### 打开文件

```python
fp = open('文件名','模式')
```

open() 用于打开文件

* 文件名： 文件的路径和名称，不能包含特殊字符
* 模式：指定文件如何被使用，r,w,a等![1565104077008](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565104077008.png)
* fp：文件句柄对象，包含了打开文件的各种状态和对文件的资源访问符

#### 关闭文件

当文件使用完毕，必须要关闭文件，否则其他程序无法使用此文件，造成资源泄露

```python
fp.close()
```

#### 读取文件

python有三种读取文件的方法:

![1565104373144](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565104373144.png)

```python
#读取所有数据
fp = open('a.tx','r')
content = fp.read()      #如果fp.read(2)就读取前两个字符
fp.close()
print(content)
```

> 注意：在很多时候读取一个文件的所有数据，会消耗大量时间和内存

```python
#读取一行
fp = open('a.txt','r')
line = fp.readline()        #lines = fp.readlines()读取所有行存放在lines列表中
fp.close()
print(line)
```

#### 二进制读写

二进制是计算机自身的语言，所以性能非常的搞，但是人类不容易理解，很多文件都是二进制文件

* 图片文件
* 视频文件
* 压缩文件
* 加密文件

上面都是二进制文件

**python是依靠bytes对象读写二进制文件，bytes对象其实就是byte数组**

> 注意：一个byte占8位，也就是byte的范围必须保持在0~255（包括255）

```python
#将数组转化为二进制
b = bytes([12,8,56])
print(b[0])    #12
print(b[1])    #8
#如果数组内有元素超过了255,获取的时候就会报错！
```

### 读写二进制数据到文件中

写入二进制数据进入文件中，首先必须以’wb‘ 或者'ab'（一个覆盖，一个追加二进制数据）模式打开文件，才可以将二进制数据录入其中

```python
fp = open("a.txt","wb")
#将列表[3,4,255]转为二进制
b = bytes([3,4,255])
print(b[0],b[1],b[2])         # 3 4 255
print(b)                      #b'\x03\x04\xff'  直接打印b发现数组的所有东西转为了16进制编码。其实还是那些数据
#现将b写入到a.txt中
fp.write(b)
print("写入成功！")
fp.close()
```

读取二进制数据操作：

```python
#读取a.txt中的二进制数据
fp = open("a.txt","rb")
#读取一行二进制数据，并将结果存入到b中
b = fp.readline()
print(b) #b'\x03\x04\xff'
print(b[0],b[1],b[2])  # 3 4 255
```

这里就完成了整个二进制文件的读写操作

