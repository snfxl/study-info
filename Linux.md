# 1.Linux安装软件（镜像源安装 - yum）

CentOS-7-x86_64-DVD-2003.iso 文件中，已经存在了大量的 内置软件包，我们可以使用yum 命令来进行镜像源软件安装！

```java
命令    解析
yum -y install [package]    下载并安装一个rpm包
yum localinstall [package.rpm]    安装一个rpm包，使用你自己的软件仓库解决所有依赖关系
yum -y update    更新当前系统中安装的所有rpm包
yum update [package]    更新一个rpm包
yum remove [package]    删除一个rpm包
yum list    列出当前系统中安装的所有包
yum search [package]    在rpm仓库中搜寻软件包
yum clean [package]    清除缓存目录（/var/cache/yum）下的软件包
yum clean headers    删除所有头文件
yum clean all    删除所有缓存的包和头文件
```

# 2.安装JDK：

```java
yum list   //罗列出所有的安装软件包
yum search 包的名字    //查询软件包
yum -y install java-1.8.0-openjdk.x86_64 //安装JDK 1.8
```

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011111103/QQ%E6%88%AA%E5%9B%BE20201111103758.png)

## 提醒

可以使用yum search 包的大概的名字，查询包的具体名字

## 查询安装位置

```java
rpm -ql 包的名字
例如：
rpm -ql java-1.8.0-openjdk.x86_64
-ql 选项  表示查询包的安装详细目录
```

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011111120/QQ%E6%88%AA%E5%9B%BE20201111103758.png)

# 3.安装tomcat

搜索tomcat包的名称：

```
yum search tomcat
```

安装具体的tomcat

```
yum -y install tomcat-webapps.noarch
```

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011111124/QQ%E6%88%AA%E5%9B%BE20201111103758.png)

查看安装位置

```
rpm -ql tomcat-webapps.noarch
```

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011111126/QQ%E6%88%AA%E5%9B%BE20201111103758.png)

### 启动tomcat

```java
systemctl start tomcat
systemctl stop tomcat
systemctl restart tomcat
```

### 通过netstat命令，可以查看端口的占用情况

```java
netstat -lnpt
或
netstat -lnpt |grep 8080
```

### 通过如下地址，可以查看tomcat

```
http://192.168.6.111:8080/
```

注意：192.168.6.111 是Linux服务器的IP地址

如果打不开，可能是因为防火墙的问题！

# 4.Linux系统的防火墙

## 检查防火墙的状态

```
systemctl status firewalld
```

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011111138/QQ%E6%88%AA%E5%9B%BE20201111103758.png)

## 改变防火墙的状态

```java
systemctl start firewalld
或
systemctl restart firewalld
或
systemctl stop firewalld
```

### 关闭防火墙之后，再次刷新界面

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011111140/QQ%E6%88%AA%E5%9B%BE20201111103758.png)
但是不推荐关闭！

### 防火墙的端口操作(添加端口)

```java
firewall-cmd --permanent --add-port=8080/tcp --permanent 
--permanent 让端口配置 永久生效
```

执行结果：

```java
[root@localhost /]# firewall-cmd --permanent --add-port=8080/tcp --permanent 
success
[root@localhost /]#
```

### 添加端口之后，需要重新刷新端口的配置

```
firewall-cmd --reload
```

### 删除某个端口

```
firewall-cmd --permanent --remove-port=8080/tcp     //从防火墙中删除8080端口
```

### 检查防火墙都开了多少个端口

```
firewall-cmd --list-all
```

执行效果：

```java
[root@localhost /]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens33
  sources: 
  services: dhcpv6-client ssh
  ports: 5672/tcp 15672/tcp 6379/tcp 8080/tcp
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
[root@localhost /]#
```

## 设置防火墙开机启动

```
systemctl enable firewalld
```

## 取消防火墙开机启动

```
systemctl disable firewalld
```

## 删除某一个yum安装的包

语法：

```java
yum remove 包的名称
例如：
yum -y remove tomcat-webapps.noarch
```

执行结果

```java
[root@localhost /]# rpm -ql tomcat-webapps.noarch
未安装软件包 tomcat-webapps.noarch 
[root@localhost /]#
```

# 5.切换镜像源

CentOS 镜像安装包，默认的安装包的路径都指向国外，为了提升下载的效率，我们需要将
国外的镜像源，修改为国内的镜像源

## 先下载wget工具

需要执行的命令

```
yum -y install wget
```

## 下载阿里的镜像源

```java
//先到默认的镜像源的 目录中去
cd /etc/yum.repos.d/
wget http://mirrors.aliyun.com/repo/Centos-7.repo
```

执行结果

```java
[root@localhost /]# wget  http://mirrors.aliyun.com/repo/Centos-7.repo
--2020-11-11 14:07:00--  http://mirrors.aliyun.com/repo/Centos-7.repo
正在解析主机 mirrors.aliyun.com (mirrors.aliyun.com)... 221.236.11.248, 171.220.234.243, 118.123.199.234, ...
正在连接 mirrors.aliyun.com (mirrors.aliyun.com)|221.236.11.248|:80... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：2523 (2.5K) [application/octet-stream]
正在保存至: “Centos-7.repo”
100%[=========================================================>] 2,523       --.-K/s 用时 0s      
2020-11-11 14:07:00 (144 MB/s) - 已保存 “Centos-7.repo” [2523/2523])
[root@localhost /]#
```

## 替换本地的镜像源

进入/etc/yum.repos.d/ 目录中去

在目录中，执行下面的命令

```java
mv CentOS-Base.repo CentOS-Base.repo.back
再
mv Centos-7.repo CentOS-Base.repo
```

## 刷新yum源

依次执行下属的3条命令：

```java
yum clean all   //清理所有的之前的安装包的缓存
yum makecache   //将现有的新的安装包 进入到缓存中去
yum update      //执行刷新
```

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011111421/QQ%E6%88%AA%E5%9B%BE20201111103758.png)

刷新完毕之后，我们在执行yum -y install 某一个软件时， 将会从国内的阿里巴巴服务器上去直接下载

# 6.下载二进制包 安装

例如：在apache.org上下载了一个 apache-tomcat-8.5.59.tar.gz

使用xshell将下载包，使用 rz 上传到/usr/local下

```java
[root@localhost local]# ll
总用量 10188
-rw-r--r--. 1 root root 10432020 11月 11 12:22 apache-tomcat-8.5.59.tar.gz
drwxr-xr-x. 2 root root        6 4月  11 2018 bin
drwxr-xr-x. 2 root root        6 4月  11 2018 etc
```

## 针对该二进制包，进行解压

```
tar -zxf apache-tomcat-8.5.59.tar.gz
```

## 启动tomcat

进入到 /usr/local/apache-tomcat-8.5.59/bin 目录中
输入

```
./startup.sh
```

## 关闭tomcat

进入到 /usr/local/apache-tomcat-8.5.59/bin 目录中
输入

```
./shutdown.sh
```

# 7.使用yum安装计划任务功能，结果提示：Another app is currently holding the yum lock; waiting for it to exit...1234

```
# yum -y install vixie-cron
Loaded plugins: fastestmirror, refresh-packagekit, security
Existing lock /var/run/yum.pid: another copy is running as pid 25960.
Another app is currently holding the yum lock; waiting for it to exit...1234
```

可能是系统自动升级正在运行，yum在锁定状态中。
已经有一个yum进程在运行了，使用kill干掉它：

```
# kill -s 9 25960
# ps aux|grep yum
root      6744  0.0  0.0 103260   900 pts/1    S+   14:59   0:00 grep yum
root     25960  0.0  0.0      0     0 ?        Z    Sep19   0:01 [yumBackend.py] <defunct>1234
```

很遗憾，kill对付不了它，那怎么办呢？

可以通过强制关掉yum进程：

```
#rm -f /var/run/yum.pid
```

然后就可以使用yum了

# 8.linux安装文件出现configure: error: no acceptable C compiler found in $PATH错误

configure: error: no acceptable C compiler found in $PATH错误主要是没有C编译器.

安装C编译器：

```
[root@localhost sipp-3.5.1]# yum -y install gcc
```

重新安装文件，问题解决

# 9.configure: error: You need a C++ compiler for C++ support.

当您的服务器能链接网络时候[联网安装gcc c++]

```
[root@localhost]# yum install -y gcc gcc-c++
```



# 10.网络连接（Vmware 软件配置）

桥接方式：以物理机的网卡为桥梁，向路由器申请一个新的网络地址
Nat方式：采用vmnet8为网卡，进行与物理机进行地址转换
主机模式：虚拟机只同物理机进行通信，不需要连接外网

### 桥接模式

#### 第一步，修改网络连接方式

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011101633/QQ%E6%88%AA%E5%9B%BE20201110103727.png)

#### 第二步，开机，并登陆

#### 第三步，进入到网络连接配置文件目录

```
cd /etc/sysconfig/network-scripts/
```

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011101635/QQ%E6%88%AA%E5%9B%BE20201110103727.png)

#### 第四步，使用vim 编辑上述的ifcfg-ens33

```
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

添加如下内容：

TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=452c1b48-748e-44af-8e3a-093c042a8313
DEVICE=ens33

ONBOOT=yes
BOOTPROTO=”static”
IPADDR=”192.168.6.90”
GATEWAY=”192.168.6.1”
DNS1=”61.139.2.69”
NETMASK=”255.255.255.0”

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011101642/QQ%E6%88%AA%E5%9B%BE20201110103727.png)

#### 第五步，重启网卡

```
systemctl restart network
```

#### 第六步，检测网络是否通畅

第一种方式：

```
ping 某一个IP地址
```

进一步监测，是否可以连接外网

```
traceroute www.baidu.com或者ping 百度的IP地址
```

![img](https://woniuxyopenfile.oss-cn-beijing.aliyuncs.com/woniuxynote/classNoteMd/202011101714/QQ%E6%88%AA%E5%9B%BE20201110103727.png)


