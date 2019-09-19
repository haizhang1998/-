### 大数据学习（三） ---- Shell编程

**第一模块：创建shell脚本**

首先我们了解下echo

* 基本语法： echo 【选项】【输出内容】 
* 选项： -e   支持反斜线控制的字符串转换

![1565947503733](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565947503733.png)

知道了echo的使用，我们来开始制作第一个shell脚本（**shell脚本以#!/bin/bash开头**）

我们touch一个**.sh为后缀的文件** ，然后在文件内部输入下面的命令

![1565947892766](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565947892766.png)

其中#!/bin/bash是必须的。



**第二模块：shell脚本的执行方式**

shell脚本有很多种执行的方式，我们先查看下创建的shell脚本文件在何处

![1565948088556](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565948088556.png)

在指定shell脚本同一级目录下，如下操作都可以执行shell脚本

* ./shell脚本全名
* sh ./shell脚本全名
* bash .shell脚本全名

如果是在别的路径下，要调用shell脚本，指定它的绝对路径即可。

* sh ./opt/test/Shell/shell脚本全名



**第三模块：shell脚本种的变量**

LinuxShell种的变量分为，系统变量/用户自定义变量，其中通过set命令进行查看系统变量

系统变量：

* $HOME 
* $PWD
* $SHELL
* $USER

下面可以查看他们的值

```shell
echo $USER
echo $系统变量名
```



**第四模块： 自定义变量**

![1565948778208](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565948778208.png)

```shell
#设置一个变量SS
SS=100
#获取变量的值
echo $SS
#声明静态只读变量,前提这个变量被声明过并赋值才有意义
readonly SS
#撤销变量unset，如果SS被声明了readonly 则不能被撤销了
unset SS
```

变量的定义规则

![1565949074481](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565949074481.png)

将变量的返回值赋值给变量

![1565949462158](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565949462158.png)

演练：

![1565949489390](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565949489390.png)