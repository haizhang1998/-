## 大数据学习----Linux篇（二）

### 进程线程类

1. ps查看系统中所有进程

`ps -aux` 可以看到当前的所有进程

![1565164631296](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565164631296.png)

`ps -aux | grep 线程ID` 查看指定id的进程

2. top [选项]查看进程

   ![1565164831775](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565164831775.png)

   `top -d 2` 每隔2秒刷新一次，按q就可以退出观测

3. pstree 查看进程树

   pstree - u  查看进程树中的进程属于谁

   pstree - p  查看进程树种的进程对应的PID

   kill 进程ID  可以终止进程。

   

###Crond系统定时任务

![1565165511159](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565165511159.png)

![1565165697790](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565165697790.png)

上面是每年的5月13号16点1分写入helloword到/home/atguigu,txt中

![1565165907080](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565165907080.png)

命令的含义

![1565166249107](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565166249107.png)

我们可以调用`serivce crond restart`重新启动定时任务

接下来来实战一次

1. 新建一个crontab任务，使用`crontab -e`

![1565166425664](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565166425664.png)

上面的意思是，每隔1分钟像a.txt输出上面的语句

2. wq进行保存任务。
3. 使用tail -f a.txt 去实时监控输出的结果



### ln 【源文件】【目标文件】 

创建源文件的硬链接。

```shell
ln a.txt b.txt
```

上面代码的含义是将a.txt文件创建一个硬链接，并命名位b.txt，其中b.txt的内容在磁盘中是真实存在的！**并且会实时同步变更**，比如a.txt突然想要增加点东西，那么b.txt也会做相应的增添！

![1565167556616](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565167556616.png)

并且删除其中一个文件（a/b.txt）另一个文件并不会受到影响





### 光盘镜像文件的挂载

![1565167858204](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565167858204.png)

![1565167874840](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565167874840.png)



### find 搜索文件

​     格式：find [路径] [参数]
​     -atime +n/-n    表示访问或者执行时间大于或小于n天的文件
​     -ctime  +n/-n 
​     -name  按照文件名字查询       （eg: find / -name *.txt   查找所有后缀位txt的文件）





### rpm

搜索

rpm -qa 

卸载

rpm -e 软件包名

安装

rpm -ivh 软件包名  (要把光盘挂在到/mnt/cdrom 然后cd到packafe上)

