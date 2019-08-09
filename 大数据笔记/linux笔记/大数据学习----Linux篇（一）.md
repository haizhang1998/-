## 大数据学习----Linux篇（一）

**安装centos6.8，省略安装流程**

####第一模块：配置centos的ip为静态ip

实例环境

虚拟机:VMware 14

系统：CentOS 6.8

1. **ifconfig -a       << 查看所有网卡的状态**

2. **vi /etc/sysconfig/network-scripts/ifcfg-eth0  << 编辑网卡配置文件**

```shell
按i键开始进行配置

要修改的内容：

标识 5 ONBOOT=no 修改为 ONBOOT=yes 

     # 是否随系统启动

标识 7 BOOTPROTO=dhcp 修改为 BOOTPROTO=static 

     # IP地址分配方式，是DHCP服务器自动分配，还是手动配置

#下面是添加的内容，ip地址和网关的前3位一致，后面位自选。网关地址在vmware的编辑-->虚拟网络编辑器-->VMware8-->net设置里面可以看到，一定要将网络模式改位NET喔，不能是桥接
 IPADDR=192.168.1.108 
 NETMASK=255.255.255.0 
 GATEWAY=192.168.1.1 
 DNS = 8.8.8.8
 DNS配置根据当地网络供应商进行添加
```

3.退出保存：

   ①按Esc键

   ②输入:wq回车

4.重启网络服务：# service network restart

DNS还可以在其他的文件进行配置

**vi /etc/resolv.conf**

```
nameserver 8.8.8.8
```

**退出保存，重启网络服务。实际 /etc/resolv.conf 是自动调用之前网卡配置文件DNS配置信息 **



**第二模块：配置主机名称**

如果想要查看主机名称可以直接键入`hostname` 即可

如果我想要修改主机名

`vi /etc/ sysconfig/network`

![1565162062649](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565162062649.png)

可以将HOSTNAME的值修改即可

**如果你想要统一配置主机名代替ip地址**

`vim /etc/hosts` 

```shell
192.168.88.101 hadoop101
192.168.88.102 hadoop102
192.168.88.103 hadoop103
192.168.88.104 hadoop104
192.168.88.105 hadoop105
192.168.88.106 hadoop106
192.168.88.107 hadoop107
192.168.88.108 hadoop108
```

这样我们就相当于给每一个ip地址起了一个新名字，如果有多台机器需要用到这个ip，我们就直接把这个名字给他即可，如果哪一天我要将hadoop101的ip修改称192.168.88.222 ，那就不必每一台机器都去修改它。只需把/etc/hosts找到对应名称修改其ip即可。

除了修改linux上的hosts，我们windows上的也要修改hosts，将上面的内容粘到windows的hosts上。

1. 进入C:\Windows\System32\drivers\etc

2. 打开hosts文件并添加如下内容

   ```shell
   192.168.88.101 hadoop101
   192.168.88.102 hadoop102
   192.168.88.103 hadoop103
   192.168.88.104 hadoop104
   192.168.88.105 hadoop105
   192.168.88.106 hadoop106
   192.168.88.107 hadoop107
   192.168.88.108 hadoop108
   ```



#### 第三模块：防火墙

`service iptables status` 查看防火墙信息

`service iptables stop`  关闭防火墙（如果下次再开机，防火墙又会被打开）

`chkconfig iptables --list` 查看防火墙开机启动状态

![1565162766049](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565162766049.png)

`chkconfig iptables off`关闭防火墙开机启动



#### 第四模块：关机重启

基本语法：

1. sync（功能描述:将数据由内存同步到硬盘中）

2. shutdown【选项】 -r  时间

   ```shell
   shutdown -r 3 "3分钟后重启"
   ```

3. halt (功能描述:关闭系统，等同于shutdown -h now 和power off)

4. reboot (功能描述:就是重启，等同于shutdown -r now)

**在关机或者重启的时候要用sync命令将重要的数据持久化到磁盘**



#### 设置系统的时间

`date -s "2017-08-19 11:33:33"`