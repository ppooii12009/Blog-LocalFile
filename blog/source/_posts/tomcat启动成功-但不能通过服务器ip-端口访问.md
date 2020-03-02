---
title: tomcat启动成功 但不能通过服务器ip+端口访问
date: 2020-02-29 14:30:04
tags: 
	- Linux那些事儿
categories: 
    - tools
---

为了安装`nextcloud`，把`centOS`一顿捣鼓之后发现各种版本`php`全都乱了，`httpd`和`nginx`的配置文件也改的面目全非，终于还是向现成的`LNMP`镜像投降了......

但重装完系统，`tomcat`也要重新安装. 然而`tomcat`启动成功之后，访问`ip:8080`的格式却看不到熟悉的`apache tomcat` 界面，只显示一个空空的 `无法显示该网页` 

用`netstat -nlp `命令能看到 `8080`端口信息如下

```powershell
[root@VM_0_13_centos bin]# netstat -nlp |grep 8080
tcp6       0      0 :::8080                 :::*                    LISTEN      20474/java          
```

这就奇了怪了，输入 `ps -ef|grep tomcat `命令，也能看到 `tomcat`启动成功

```powershell
[root@VM_0_13_centos ~]# ps -ef |grep tomcat
root     20474     1  0 13:59 pts/0    00:00:04 /usr/bin/java -Djava.util.logging.config.file=/usr/local/apache-tomcat-8.5.51/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Dignore.endorsed.dirs= -classpath /usr/local/apache-tomcat-8.5.51/bin/bootstrap.jar:/usr/local/apache-tomcat-8.5.51/bin/tomca-juli.jar -Dcatalina.base=/usr/local/apache-tomcat-8.5.51 -Dcatalina.home=/usr/local/apache-tomcat-8.5.51 -Djava.io.tmpdir=/usr/local/apache-tomcat-8.5.51/temp org.apache.catalina.startup.Bootstrap start
root     23218 11705  0 14:13 pts/0    00:00:00 grep --color tomcat
```

然后一顿上网搜索，先确认一下 `8080`端口是否被开启，在启动防火墙时却意外遇到问题

```shell
[root@VM_0_13_centos ~]# firewall-cmd --state
not running
[root@VM_0_13_centos ~]# systemctl stop firewalld.service 
[root@VM_0_13_centos ~]# systemctl start firewalld
Failed to start firewalld.service: Unit is masked.
```

发现报错 `Unit is masked`，又一顿 search，只要把锁定状态解除就好

```shell
[root@VM_0_13_centos ~]# systemctl unmask firewalld
Removed symlink /etc/systemd/system/firewalld.service.
[root@VM_0_13_centos ~]# systemctl start firewalld
[root@VM_0_13_centos ~]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: active (running) since Sat 2020-02-29 14:16:55 CST; 7s ago
     Docs: man:firewalld(1)
 Main PID: 23957 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─23957 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

Feb 29 14:16:55 VM_0_13_centos systemd[1]: Starting firewalld - dynamic firewal....
Feb 29 14:16:55 VM_0_13_centos systemd[1]: Started firewalld - dynamic firewall....
Feb 29 14:16:56 VM_0_13_centos firewalld[23957]: ERROR: Failed to read file "/p...'
Feb 29 14:16:56 VM_0_13_centos firewalld[23957]: WARNING: Failed to get and par...g
Hint: Some lines were ellipsized, use -l to show in full.
```

然后用 `firewall`相关命令开启 `8080`端口

```shell
[root@VM_0_13_centos ~]# firewall-cmd --zone=public --add-port=8080/tcp --permanent
success
```

再检测端口是否开启

```shell
[root@VM_0_13_centos ~]# firewall-cmd --permanent --zone=public --list-ports
8080/tcp
[root@VM_0_13_centos ~]# firewall-cmd --zone=public --query-port=8080/tcp
yes
```

稳妥起见重载一下防火墙

```shell
[root@VM_0_13_centos ~]# firewall-cmd --reload
success
```

再次访问 `ip:8080`地址，终于能看到熟悉的界面了

<div align = center>
	<img src = "https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/tomcat%E5%90%AF%E5%8A%A8%E6%88%90%E5%8A%9F%20%E4%BD%86%E4%B8%8D%E8%83%BD%E9%80%9A%E8%BF%87%E6%9C%8D%E5%8A%A1%E5%99%A8ip%2B%E7%AB%AF%E5%8F%A3%E8%AE%BF%E9%97%AE/tomcat.png">
    <center class = "caption">终于看到了 tomcat 启动默认界面</center>
</div>

