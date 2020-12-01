# 1.nginx高可用（主备配置）

```java
#全局配置
global_defs {

 notification_email {

 acassen@firewall.loc

 failover@firewall.loc

 sysadmin@firewall.loc

 }

 notification_email_from Alexandre.Cassen@firewall.loc

 smtp_server 192.168.17.129 #要监控的本机的ip

 smtp_connect_timeout 30

 router_id LVS_DEVEL 
     #主机名称 vim /etc/hosts 配置 127.0.0.1 LVS_DEVEL

}

#脚本配置
vrrp_script chk_http_port {
 	#检测当前nginx服务器是否可用的脚本
 	script "/usr/local/src/nginx_check.sh" 
    #（检测脚本执行的时间间隔每隔两秒）
 	interval 2 
	#设置当前服务器的权重
 	weight 2 
}

#虚拟ip配置
vrrp_instance VI_1 {
	
# 备份服务器上将 MASTER 改为 BACKUP  表示当期服务器是主服务器还是# 配置服务器
 state BACKUP 
# 网卡
 interface ens33
# 主、备机的 virtual_router_id 必须相同 相当于一个标识
 virtual_router_id 51 
# 主、备机取不同的优先级，主机值较大，备份机值较小
# 主机一般100 备份机小于100即可
 priority 100
#每隔多久发送一次心跳 1秒
 advert_int 1

#校验权限的一种方式
authentication {

 	auth_type PASS

 	auth_pass 1111

 }

 #VRRP H 虚拟地址  可以绑定多个   
 virtual_ipaddress {

 	192.168.17.50

 } 

}
```

# 2.nginx_check.sh脚本

/usr/local/nginx/sbin/nginx 启动脚本的位置

```java
#!/bin/bash

A=`ps -C nginx –no-header |wc -l`

if [ $A -eq 0 ];then

 	/usr/local/nginx/sbin/nginx

 	sleep 2

 	if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then

 		killall keepalived

	 fi
fi
```

