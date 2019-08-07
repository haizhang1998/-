## 大数据学习----Python(三)

#### 列表

**创建列表**

python中有两种列表的创建方式

```python
    #直接写
 l  =  [12,3,4]
    #构建一个list
 l  =  list([1,2,3])
```

**访问元素**

```python
l=[1,2,3,4,5]
print(l[3:5])      # 3，4 不包含位置5的元素
```

和字符串的玩法一样

**list的一些常用操作**

![1565079870711](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565079870711.png)

![1565079933228](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565079933228.png)

![1565079966706](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565079966706.png)

**list比较运算**

其原理和str比较运算一样，从第0个开始，一个个往后比较

```python
[12,5,6]>[39] 	 #False
[1,2,3]>[]	     #True
```

**list独有操作**

![1565080349679](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565080349679.png)

```python
l = list ([1,2,3,4,5])
l.append(2)                #数组末尾追加2 
print(l) 				   #[1, 2, 3, 4, 5, 2]

l.pop(i)                   #删除数组的第i个元素并将删除的值返回
l.pop()                    #删除数组最后一个

l.remove(x)                #删除值为x的元素,从最左边查找删除匹配的第一个
l.reverse()  			   #反转数组，没有返回值

l.sort() 				   #从小到大排序数组
```





#### Python推导式（重点）

![1565081113703](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565081113703.png)

python的推导式类似于我们常用的过滤器，定义一些过滤规则，对你想要进行过滤的集合进行过滤操作，分别遍历集合的每个元素，进行过滤判断，如果过滤成功后，还想做一些处理逻辑，那就再过滤的元素添加逻辑，并最终将结果返回在另一个新的集合内部

我们来深入比较推导式的神奇之处！

现在有个需求，判断一个链表中的元素是否为奇数，如果是就将其加5，并且将所有满足条件并处理过后的结果放在一个新数组保存

```python
#这里是没有运用推导式的代码
l = [1,23,5,6,7]
l2 = [];
for item in l:
    if item%2!=0:
        l2.append(item+5)
print(l2)
```

```python
#这里是运用推导式的代码
l = [1,23,5,6,7]
l2 = [item+5 for item in l if item%2!=0]
print(l2)
```

结果显然，运用推导式的代码比没有运用推导式的代码整整节省了一半的代码量！ 如果逻辑更加复杂，那么推导式会呈现的更加简洁逻辑清晰

我们必须在合适的场景运用推导式去简化我们的代码。