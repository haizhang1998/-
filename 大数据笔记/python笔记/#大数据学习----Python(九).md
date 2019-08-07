# 大数据学习----Python(九)

### Python的对象和类

对象和类是所有高级语言必须的特征。

* 类：设计图，不具有实际的功能，需要实例化才可用
* 对象（实例）:由类创建出来的铲平，内容由类规定，可以使用

例如：对于list而言

* list就是类，你无法位list添加任何东西
* l=list()就是一个实例，同时你可以位这个实例添加东西（l.append（13））

#### 创建类和对象

Python是一门面向对象的语言，一切都是对象，比如int,str，函数都是对象；面向对象意味着用对象来构建程序

**类的定义**

Python使用class关键字定义类

> 习惯上，类首字母大写

类由数据（属性）和行为（方法）组成

> 属性和方法，统称为成员

```python
class Person:    #定义一个类
    #类的属性
    name=''
    age=0
    
    #类的方法，构造方法
    def __init__(self,name,age):
        #self相当于java中的this关键字
        self.name = name
        self.age = age
        
    def show(self):
        print('姓名:'+self.name)
        print('年龄:'+str(self.age))
        
#类的创建
p=Person('张三',35)
#self不用传默认是对象本身，当前实例的引用
p.show()
```

上面案例中，self就是此对象的引用，相当于java的this。在传参数的时候不用传self的参数，Python中并没有new关键字，如果你想要创建一个实例，就直接将类的名字声明调用，和调用方法一致。

>  注意：self必须要存在类中方法的参数中，id(self)就是当前对象的id值

#### 私有成员

使类中成员无法在外部被访问，从而保护私有成员

> Python其实没有真正的private

```python
class Person:
    #公有变量，外部使可以访问的
    name = ''
    #私有变量，外部不可访问
    __email=''
    
    def __init__(self,name,email):
        self.name=name
        self.__email=email

p=Person('haizhang','221312@qq.com')
p.__email  #报错，私有变量不可直接访问
p._Person__email   #通过，可以以这种方式访问私有变量
```

声明私有变量在变量之前加上 __ 两条下划线 

我们依旧可以换一种方式运用 p._Person__email去访问p对象中的私有变量，

> Python中没有真正的私有变量，只是把变量隐藏了起来而已





### Python操作符重载

什么使操作符重载呢？加号（+）即可以用于数字相加，也可以用于字符串丽娜姐，就是一种重载，我们可以定义自己的重载。比如加号，其实是__add__方法

```python
class Circle:
    __radius = 0 
    def __init__(self,radius):
        self.__radius=radius
    
    def show(self):
        print('半径:'+str(self.radius))
        
    def __add__(self,other):
        return Circle(self.__radius+other.__radius)
    
c1 = Circle(1)
c2 = Circle(2)
c3=c1+c2    #也可以c3=c1.add(c2)
c3.show()   #半径:3
```

上面的Circle类中，+操作就是__add__方法起到了作用，只要修改重写其中的逻辑，就可以按照你的逻辑去走，也就支持了+操作了

python的其他操作符重载需要重写的函数

![1565142006699](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565142006699.png)

其中str()就是相当于java的toString，直接打印实例的时候，会将实例的地址打印出来，我们重写__str__ 就可以打印出我们想要的东西。

```python
class Person:  # 定义一个类
    # 类的属性
    name = ''
    age = 0

    # 类的方法，构造方法
    def __init__(self, name, age):
        # self相当于java中的this关键字
        self.name = name
        self.age = age

    def show(self):
        print('姓名:' + self.name)
        print('年龄:' + str(self.age))

    def __gt__(self, other):
        if self.age > other.age:
            print(self)
        else:
            print(other)
    def __str__(self):
        return self.name

# 类的创建
p = Person('张三', 35)
p2 = Person("里斯",23)
#运算符重载，调用__gt__方法
p>p2
```





### Python继承和多重继承

* 父类，超类（super class），基类（base class）：被继承的类
* 子类(subclass) 派生类

创建一个类继承另一个类

```Python
class Person:  # 定义一个类
    # 类的属性
    name = ''
    age = 0

    # 类的方法，构造方法
    def __init__(self, name, age):
        # self相当于java中的this关键字
        self.name = name
        self.age = age

    def show(self):
        print('姓名:' + self.name)
        print('年龄:' + str(self.age))
        
#定义一个子类继承父类Person,只需要Worker声明时用(Person)表明继承关系
class Worker(Person):
    #worker的拓展属性，自己的属性，不属于父类
    job=''
    #父类只由2个参数的构造器，所以必须要传入name，age
    def __init__(self,name,age,job):
        #首先要调用父类的构造方法super()指向父类
        super().__init__(name.age)
        self.job = job
        
    
       
```

子类的构造函数必须要调用父类的构造函数。

super（）是一个方法，通过方法访问父类。

子类会继承父类的所有共有方法。

### super关键字

super用于访问父类（super class）的成员

> 本质上，super访问的是父类的方法以及属性数据

```python
class Person1:
    name = ''

    def __init__(self,name):
        self.name = name
        print("调用父类Person1构造方法:"+self.name)

    def __priveteMethod(self):
        print("Person1 privateMethod")

    def show(self):
        print("Person1:name:"+self.name)

class Man(Person1):
    name = ""
    def __init__(self,name):
        #调用父类构造方法super()方法访问父类
        super().__init__(name)
        #尝试去调用Person1私有方法
        super()._Person1__priveteMethod()
        #去调用父类的show方法
        super().show()
        #子类调用show方法
        self.show()

    #重写父类show方法
    def show(self):
        print("子类Man重写show方法"+self.name)
man = Man("haizhang")
```

我们看到Man重写了show方法，那么打印的结果到底是什么呢

![1565144637763](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565144637763.png)

很显然子类构造方法打印的结果，如果子类重写了父类的show方法，那么self.show（）就不再时从父类继承下来的逻辑，而是子类自己的逻辑，如果你还想使用父类的show方法，就需要super().show（）。super()就是去获取父类对象，直接去访问父类的逻辑即可。

> 不同于java，python中的super不是一个关键字，而是一个方法，它不规定必须要在构造方法中使用，可以在任意的方法中进行调用super()访问父级的成员。而且super()在构造方法中不一定要写在第一位，放在任意处都可以

**多重继承**

从多个父类同时继承，交多重继承，多重继承本身是否合理是语言界的一个争论

* 支持的:C++,Python
* 不支持:java,c#,PHP,ruby...

```python
class Parent1:
    a=12

class Parent2:
    b=5
    
class Child(Parent1,Parent2):
    def __init__(self):
        pritn(self.a)
        print(self.b)
c=Child() #12,5

```

现在如果Parent1和Parent2有两个变量名字相同，那值到底是谁的么？

```python
class Parent1:
    a=12

class Parent2:
    a=5
    
class Child(Parent1,Parent2):
    def __init__(self):
        pritn(self.a)
       
c=Child() #12   哪个父类在前，值就是哪一个的
```

其实区分多个父类中的成员（如果同名的话）由以下图的顺序

![1565143416053](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565143416053.png)

上面的程序我们如果想要访问Parent2中a的值怎么办呢？

```python
class Parent1:
    a=12

class Parent2:
    a=5
    
class Child(Parent1,Parent2):
    def __init__(self):
       print(super(Parent1,self).a)#5
       
c=Child() #5
```

什么意思呢，当print(super().a)的时候，访问的是第一个父类Parent1

中的a，由上面的顺序图，Parent2排在Parent1后面，想要访问Parent2的a属性就要将Parent1和当前实例传递到super中，表明Parent1下面的父类中寻找a属性的值。



#### 重写父级方法

父类的方法会直接继承给子类，但也可以在子类中覆盖掉父级的方法，只要子类重写父类的方法逻辑，那么子类对象调用父类的方法时走的就是子类重写的逻辑。



#### isinstance(obj,class)函数

检测某一个对象是否为某个类的实例 ，相当于java的instanceof

```python
l = [1,2,3]
print(isinstance(l,object))  #True
print(isinstance(l,dict))    #False
```

所有的对象都是object的实例。

