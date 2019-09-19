## hadoop学习（二）----Hadoop搭建环境

1. 克隆虚拟机

   前提必须保证要克隆的虚拟机处于关机状态！ 

   ![1566198659047](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566198659047.png)

根据向导依次点击下一步

![1566198703509](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566198703509.png)

到这一步要选择创建完整克隆，不然只是个软连接而已！选择指定克隆机存放的位置

2. **修改物理地址**

克隆完虚拟机，打开克隆的虚拟机，然后修改物理地址

```shell
#切换到root
sudo root
vim /etc/udev/rules.d/70-persistent-net.rules
```

将上面的文件的内容修改大致如下的样子，你只需要删除倒数第二个注释和倒数第一个注释之间的内容即可

![1566199350663](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1566199350663.png)

然后将ATTR{address}==中的内容地址（02:0c:29:16:21:80）右击copy，并将NAME="eth1"改为NAME="eth0"即可，wq!保存退出

3. 修改