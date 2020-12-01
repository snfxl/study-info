# 一、Windows 解决 Nacos-1.2.1 无法链接 MySQL-8.0.17

### 先将 Nacos 项目源码下载到本地

方式一（GitHub）：git clone [git@github.com](mailto:git@github.com):alibaba/nacos.git

方式二（Gitee）：git clone [git@gitee.com](mailto:git@gitee.com):mirrors/Nacos.git

### 修改配置打包替换原有jar

修改“~/nacos/pom.xml”

```
<!-- JDBC libs -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>8.0.17</version>
</dependency>
```

修改“~/nacos/naming/src/main/java/com/alibaba/nacos/naming/healthcheck/MysqlHealthCheckProcessor.java”

```
修改前
import com.mysql.cj.jdbc.jdbc2.optional.MysqlDataSource;

修改后
import com.mysql.cj.jdbc.MysqlDataSource;
```

重新编译打，参见“~/nacos/BUILDING”

```
# 进入到项目根目录，执行以下命令
$ mvn -Prelease-nacos -Dmaven.test.skip=true clean install -U
```

复制最新打包的 jar 文件，去覆盖自己下载的 nacos-1.2.1 中jar

```
# 复制以下 jar 文件
~/nacos/distribution/target/nacos-server-1.2.1/nacos/target/nacos-server.jar

# 被覆盖的目标 jar 文件
~/nacos-server-1.2.1/target/nacos-server.jar
```

### 创建数据库及增加配置

在MySQL中创建好数据库“nacos_config”，自行在代码仓库中找脚本

在“~/nacos-server-1.2.1/conf/application.properties”增加以下配置项

```
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&serverTimezone=Asia/Shanghai
db.user=MySQL登录账号
db.password=MySQL登录密码
```

### 启动测试效果

启动“~/nacos-server-1.2.1/bin/startup.cmd”，可视化界面操作数据均可正常与 MySQL-8.0.17 交互

Nacos 默认可视化
网址：http://localhost:8848/nacos
账号：nacos
密码：nacos

# 二. linux直接启动nacos报错

直接使用 ./startup.sh 默认是以集群的方式启动

```
   ERROR read cluster conf fail
```

解决方式(修改启动方式)：

```
 	./startup.sh -m standalone
```



# 三.linux配置nacos切换数据库

-  备份一份  application.properties.bk

-  添加配置

  ```
  spring.datasource.platform=mysql
  
  db.num=1
  db.url.0=jdbc:mysql://localhost:3306/nacos_config?characterEncoding=utf-8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
  db.user=root
  db.password=snbsnfxl
  ```

  