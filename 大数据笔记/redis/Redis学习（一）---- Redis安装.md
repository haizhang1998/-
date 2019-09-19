### Redis学习（一）---- Redis安装

### 一、Redis在Centos7上面安装步骤：

#### 1、下载redis安装包及安装相关依赖包

1.1下载最新稳定版的redis：

```
wget https://github.com/antirez/redis/archive/5.0.2.tar.gz
```

1.2安装依赖包：

```
yum install -y epel-release
yum install -y gcc
```

#### 2、解压及编译

2.1进入下载目录并解压：

```
cd /root
tar -xzvf redis-5.0.2.tar.gz
2.2编译：
cd redis-5.0.2
cd deps
make jemalloc
make hiredis
make linenoise
make lua
cd ..
make
make install
```

#### 3、修改配置文件

3.1打开配置文件：

```
cd /root/redis-5.0.2
vi redis.conf
```

3.2修改允许远程访问：

```
bind 127.0.0.1
修改为
#bind 127.0.0.1
```

3.3设置密码：

```
# requirepass foobared
修改为
requirepass password(需要设置的密码)
```

3.4进程在后台运行：

```
daemonize no
修改为
daemonize yes
```

3.5日志输出文件等信息：

```
logfile ""
修改为指定的日志文件
logfile "/var/log/redis/6379.log"
```

#### 4、设置启动服务

4.1将第三步配置好的配置文件复制到指定目录

```
cp /root/redis-5.0.2/redis.conf /etc/redis/redis.conf
```

4.2设置启动服务脚本

```shell
cat > /usr/lib/systemd/system/redis.service <<-EOF
[Unit]
Description=Redis 6379
After=syslog.target network.target
[Service]
Type=forking
PrivateTmp=yes
Restart=always
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli -h 127.0.0.1 -p 6379 -a jcon shutdown
User=root
Group=root
LimitCORE=infinity
LimitNOFILE=100000
LimitNPROC=100000
[Install]
WantedBy=multi-user.target
EOF
# 使服务自动运行
systemctl daemon-reload
systemctl enable redis
# 启动服务
systemctl restart redis
systemctl status redis
```





### 二 、在Windows中安装redis

## 一：下载

### 下载地址：

```
windows版本：
    https://github.com/MSOpenTech/redis/releases
Linux版本：
    官网下载：
        http://www.redis.cn/
    git下载
        https://github.com/antirez/redis/releases
```

我们现在讨论的是windows下的安装部署，目前windows下最新版本是：3.2.100。
下载地址，提供多种下载内容，
Redis-x64-3.2.100.msi是在windows下，最简单的安装文件，方便，直接会将Redis写入windows服务。
Redis-x64-3.2.100.zip是需要解压安装的，接下来讨论的是这种。
Source code (zip) 源码的zip压缩版
Source code (tar.gz) 源码的tar.gz压缩版
![图片1](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141835970-345229799.png)

## 二：安装

### 解压安装

将下载的Redis-x64-3.2.100.zip 解压到某个地址。
![解压之后存放的地址](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141837188-1772743982.png)

### 启动命令

通过cmd指定到该redis目录。
使用命令：redis-server.exe 启动服务
![启动服务](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141837907-542406591.png)
出现这种效果，表明启动服务成功。

启动另一个cmd，在该redis目录下，使用命令：redis-cli.exe 启动客户端,连接服务器
![启动客户端](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141838704-1847088572.png)
出现这种效果，表明启动客户度成功。

## 三：部署

由于上面虽然启动了redis服务，但是，只要一关闭cmd窗口，redis服务就关闭了。所以，把redis设置为一个windows服务。
安装之前，windows服务是不包含redis服务的 ![安装之前的服务](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141839766-1468591887.png)

### 安装为windows服务

安装命令: redis-server.exe --service-install redis.windows.conf 使用命令，安装成功，如图所以： ![安装命令](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141840173-403260234.png)

最后的参数 --loglevel verbose表示记录日志等级
安装之后，windows目前的服务列表 
![安装之后的服务](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141841282-1545366890.png)

注意如果出现 redis-server既不是内部命令什么的提示，那就是你的环境变量没有设置，打开环境变量，最佳redis安装目录的路径到path变量中。

### 常用的redis服务命令。

卸载服务：redis-server --service-uninstall

开启服务：redis-server --service-start

停止服务：redis-server --service-stop

重命名服务：redis-server --service-name name

重命名服务，需要写在前三个参数之后，例如：

```
The following would install and start three separate instances of Redis as a service:   
以下将会安装并启动三个不同的Redis实例作服务：

redis-server --service-install --service-name redisService1 --port 10001

redis-server --service-start --service-name redisService1

redis-server --service-install --service-name redisService2 --port 10002

redis-server --service-start --service-name redisService2

redis-server --service-install --service-name redisService3 --port 10003

redis-server --service-start --service-name redisService3
```

## 四：测试

### 启动服务

redis-server --service-start

### 客户端

命令：

```
精简模式：
redis-cli.exe
指定模式：
redis-cli.exe -h 127.0.0.1 -p 6379 -a requirepass
(-h 服务器地址  -p 指定端口号 -a 连接数据库的密码[可以在redis.windows.conf中配置]，默认无密码)
```

### 测试读写数据

![测试数据读写](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141841876-396426521.png)

安装测试成功。

 

## 五：Redis桌面管理工具

### 推荐使用的桌面管理工具：

Redis Desktop Manager

![img](https://images2015.cnblogs.com/blog/564792/201703/564792-20170302141842829-1478485599.png)

下载地址：

```
https://redisdesktop.com/download
```