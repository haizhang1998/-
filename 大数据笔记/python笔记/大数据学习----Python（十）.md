# 大数据学习----Python（十）

### Python异常处理

**什么是异常(Exception)**

本质上，异常就是操作系统发送的信号，所以并不是所有情况都能通过异常处理

异常就是运行时的错误，意外的情况，无法单纯的通过代码处理（比如用户输入错误，断网了，磁盘空间不够等）



#### 异常处理

python通过 **try....except...** 来处理异常

```python
try:
    #可能产生异常的代码
except <异常类型1>
	#处理逻辑
except <异常类型2>
	#处理异常的代码
....
```

例子：

```python
try:
    number = int(input("请输入一个数字:"))
    number2 =int(input("请输入另一个数字:"))
    print(str(number+number2))
except ValueError:
    print("输入一个数字！必须为数字！")
finally:
    print("finally语句")

```

对上面的例子,程序会先运行try块里面的程序逻辑，如果期间出现异常就会抛出去，这里抛的是ValueError，在except中进行捕获，并打印出提示信息，如果我们想要再往外抛出异常并打印栈信息，需要用raise关键字。

我们也可以为我们接受到的异常起别名，

```python
except ValueError as e:
      print("输入一个数字！必须为数字！")
      raise e
```



#### 抛出异常

你可以主动抛出异常（java中throw） 

**raise 异常对象**

```python
try:
    if int(input("age:"))>200:
        raise ValueError
    else:
        print("age valid")
except ValueError:
    print("捕获到了抛出的异常")
finally:
    print("finally")
```

![1565145708706](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565145708706.png)



#### 创造自己的异常

```python
class AgeError(RuntimeError):
	__msg=''
	
	def  __init__(self,msg):
	    super().__init__()
	    self.__msg=msg
	    
	def __str__(self):
		return self.__msg
	 
```

上面定义了一个自己的异常，所有异常都是继承**RuntimeError**的

```python
class AgeError(RuntimeError):
    __msg =""

    def __init__(self,msg):
        super().__init__()
        self.__msg=msg
    def str(self):
        return  self.__msg
try:
    if int(input("age:"))>200:
        raise AgeError("年龄不许超过200")
    else:
        print("age valid")
except AgeError as e:
    print("捕获到了抛出的异常"+e.str())
    raise e
finally:
    print("finally")
```

运行结果![1565146507879](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565146507879.png)



#### Python的模块

模块化是所有高级语言的重要特性

在python中 每一个py文件就是一个模块，我们可以运用import关键字在另外一个py模块中使用目标py文件中的成员变量

```python
#model1.py
age = 1
name ="dasda"
def show():
    print("show up :"+str(age)+":"+name)

class Person:
    __height = 2
    def __init__(self,height):
        self.__height=height

    def show(self):
        print(self.__height)

```

现在model2.py想引入model1.py中定义的成员变量

```python
#model2.py
import model1 as m
#调用model1中的show方法
m.show()
#嗲用model1中的类
p = m.Person(172)
p.show()
```

我们发现import导入模块的时候是将模块的所有成员导入，并且调用的时候必须带上模块名

我们可以使用**from...import....** 从目标模块中导入我们想要的变量。

```python
#分别导入模块1中的属性，方法，以及类
from model1 import name,show,Person
print(name)
show()
p = Person(172)
p.show()
```



### dir（）方法

用于查看模块，类的全部成员

dir(list) 

```python
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

上面就成列了所有的list拥有的成员。





拓展pycharm设置导入模块不标红

以下是解决办法

Let's do it ...

![img](https://img2018.cnblogs.com/blog/1626773/201904/1626773-20190415174703502-1161936736.png)

进入设置，找到Console下的Python Console，勾选选项“Add source roots to PYTHONPAT” 

然后点击OK

接下来如下图，右击文件所在的目录，然后“Mark Directory as”为“Sources Root”。之后当前程序所在目录颜色会变化，表示已经完成标记。 

![img](https://img2018.cnblogs.com/blog/1626773/201904/1626773-20190415175137130-521427827.png)

 之后导入程序部分下方的波浪线就会消失，将光标键入模块名，按“Ctrl+Alt+B”可以对对源程序进行查看，是不是很方便呀

 ![img](https://img2018.cnblogs.com/blog/1626773/201904/1626773-20190415175413502-611010790.png)