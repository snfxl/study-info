# linux删除自带jdk并重新安装

1，rpm -qa | grep java  //查看linux已安装的jdk

2，rpm -e --nodeps java-1.6.0-openjdk //卸载linux自带的jdk

3，上传jdk至linux

![img](https://img-blog.csdnimg.cn/20191014144114983.PNG)

4，tar xzvf jdk-7u25-linux-x64.tar.gz -C /usr/java/    //解压至指定文件夹

5，a）在/etc/profile文件下添加

```java
export JAVA_HOME=/usr/java/jdk1.7.0_25

export CLASSPATH=/usr/java/jdk1.7.0_25/lib

export PATH=$JAVA_HOME/bin:$PATH
```

   b）source /etc/profile //重载配置文件，避免重启系统

6，echo $JAVA_HOME

   echo $PATH

   java -version  //成功