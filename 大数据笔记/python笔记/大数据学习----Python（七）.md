# 大数据学习----Python（七）

Python中有两种循环：for循环和while循环

```python
#for循环
for 变量 in 可迭代对象
	语句
    
# while循环
while 条件
	语句
```

> 区别：理论上两个功能都一样，但又些许区别
>
> * for: 着重遍历所有的元素
> * while：着重按条件循环遍历



#### 可迭代对象

python常见的可迭代对象：

* 序列对象：list , tuple , 字符串
* key-value 对:   dict , set
* range对象（实际上是个list）

```python
#range本身就是可迭代的序列
list(range(1,10))      #[1,2,3,4,5,6,7,8,9,10]

#从0开始到10
for i in range(10) 
	print(i)          #遍历0到10
    
#range的步长为2，每次i+2
for i in range(1,10,2) 
    print(i)              #1,3,5,7,9
```



#### While循环

while循环着重于‘"条件是否成立"，通常来说没有固定的次数

现在需求输入密码必须8位以上，如果少于8位，要继续输入，否则打印密码

```python
def show():
    name =input("password step (8):")
    while len(name)<8:
        name = input("password step (8):")
    print("you password is : "+name)

show()
#结果
#password step (8):21
#password step (8):123
#password step (8):13123
#password step (8):1234566789
#you password is : 1234566789
```



#### 循环和跳过

我们可以用break跳出循环，用continue跳过处理逻辑，继续下一个循环

```python

def show():
    for i in range(1,10,2):
        if i == 7:
           break
        else:
            print(i)
show()
#结果：
1
3
5
```

将上面break变为continue，将会打印出1，3，5，9





#### Python的数学函数

![1565092572070](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565092572070.png)

```python
print(round(2.131235,3))  #打印结果： 2.131 因为第4位是2<5故不进1
```



#### math模块

**模块：**相当于java的一些jar包，可以直接运用里面的接口

如何导入模块：**import 模块名**

```python
import math

#cell就是向上取整 : 4
print(math.cell(3.5))
#floor就是向下取整 ：3
print(math.floor(3.99999))
#sqrt(3)表示3**0.5
print(math.sqrt(3))
#运用弧度作为sin的单位 0 --2 PI 
print(math.sin(90*math.pi/180))  ---》1.0
```

