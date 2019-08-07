# 大数据学习----Python（二）

### Python数据类型

python中存在6种数据类型

* 数字: int , float
* 字符串：str
* 列表:list
* 元组:tuple
* 字典:dict
* 布尔值:bool



**python中的三种数字**

* int : 整数，比如：23
* float: 浮点数,小数. 比如：15.8
* complex: 复数,比如：根号-1

如何检测一个变量的类型：

> type(a)  返回的就是类型。注意python3没有long，也用不着long，而python2是有long的

在python中只要带了小数点的就是浮点数（float类型），**python没有double**

**浮点数只能存近似值不能存本身**

```python
#原始的a的值
a = 123123.312380219830291803981093
#把a的值打印出来
print("{:.20f}“.formate(a))
#打印结果
123123.31238021982426289469
```

你会发现123123.3123802198之后的数字就不同了，这就正式了浮点数只可以存放近似值的原因

**复数**

```python
a= 15 +3j
b= 8 -2j
print(a+b+9)
#结果（32+1j）
```

实部虚部分别相加，并将加的实数加入到实部中



#### python运算符

基本运算符（+-*/） 和其他语言一样

**整除**

```python
#整除
6//3             --->2
5//4             --->1
#正常除法
5/4              --->1.25
```

**幂操作**

```python
#3的100次法，**求幂
print(3**100)
#可以开根号
print(3**0.5)
```

**python没有i++以及i--这样的操作**

```python
i+=1            ---->等同于i++
i-=1            ---->等同于i--
```



#### Python字符串

python中的字符串就是一串字符

* 用单引号或者双引号包围

* Python中没有单独的字符类型（c和java有char）1个字符也是字符串，没有字符也算字符串

  > 注意:字符串本质上是'一串'字符串，列表也是‘一串的东西’，所以字符串很多操作和list/tuple是通用的，Python称之为sequence(序列)



**字符串运算符**

1. 索引

   ```python
   #字符串操作符
   a = "abc"
   print(a[0]);     #获取第一个字符串
   print(type(a[0]))  #查看第一个子串类型
   #结果
   a
   <class 'str'>
   ```

2. 连接

   ```python
   a="abc"
   b="abw"
   print(a+b)      #abcabw
   ```

   注意不能字串与数字相加！

3. 重复

   ```python
   print("abc"*3)       #abcabcabc
   ```

   乘多少重复多少遍

4. 字符串截取

   1. 截取起点，终点（不包含结束位置）

      ```python
      s1 = "abcdef"
      print(s1[0:3])    #'abc'
      ```

   2. 起点

      ```python
      str1 = "012345"
      str1[3:]          #'345' 从起点一致到重点的子串
      ```

   3. 终点

      ```python
      str1='0123456'
      str1[:5]           #'01234' 从下标为0开始一致到5（不包括位置5）
      ```

   4. 全部

      ```python
      str1="01234"
      str1[:]            #'01234'全部截取
      ```



#### 字符串编码

任何的字符在计算机中，都会表示为一个数字，这就是字符编码（utf-8,unicode(万能编码...)）

**python操作编码**

字符-->编码-->ord()

```
ord('A')   ----> 65
ord('a')   ----> 97
```

编码-->字符-->chr()

```python
chr(97)    ----> 'a'
char(32599)  ---->"罗"
```

![1565073085671](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565073085671.png)

> 注意： ord()括号内只能填一个字符喔



####in和not in

相当于某个字符是否在(in)/不在（not in）某个字符串内

```python
s1 = " abc"
'a' in  s1      #True
'A' not in s1   #True

s2 = "Chrome firefox"
"Chrome"  in  s2    #True
"haizh"   in  s2    #False
```



#### 字符串的比较

字符串可以用 > , < ,>= ,<= ,==来进行比较

* 这种本质上就是比较字符串编码，比如 ord('a' )< ord('b‘)  故此’a‘<'b'
* 先比较第0位字符，如果相等继续往后比较，直到不等，再转为字符串编码比较编码的大小再返回结果
* “”空字符串第0位就是0，最小了



#### 字符串循环

```python
for s "abcd"
   print(s)                   #循环结果 a,b,c,d
```

循环遍历abcd的每个字符，然后s来引用每个字符的值，并打印出



####字符串的检测

![1565073848176](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565073848176.png)



#### 字符串的搜索

```python
s1 = "absa"
print(s1.find('w'))            # -1 找不到返回-1
print(s1.find('a'))      	   # 0 会寻找第一个a的下标返回
print(s1.rfind('a'))           # 3 会从右侧寻找第一个a的下标返回
s1.count("a")    #2 计算a出现了多少次在s1中

```

![1565078882386](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565078882386.png)

####字符串转换

![1565078917945](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565078917945.png)

```python
str = "aBc"  
str.upper()             #字符串变为大写"ABC"
str.lower() 			#字符串变为小写"abc"
a = "abaaaac"
print(a.replace('a','w'))   #wbwwwwc
```

