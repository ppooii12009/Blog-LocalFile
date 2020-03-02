---
title: modify nextcloud configurations in docker
date: 2020-03-02 19:19:00
tags:
	- Linux那些事儿
categories:
	- tools
cover: https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/modify%20nextcloud%20configurations%20in%20docker/Nextcloud.png
---

利用 docker 配置好 nextcloud 之后，需要修改一些配置文件. 但 nextcloud 是运行在 docker 的容器内部，并不会在直接的系统路径下生成对应文件，所以找 `config.php` 文件着实费了一阵子劲 ~~甚至为了换成 mysql 还重新 pull 了一遍 nextcloud 镜像 (捂脸)~~

修改配置文件实质上就是进入对应的容器内部，而容器本质上是一台虚拟机，利用 docker 提供的命令

```shell
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

其中

- `OPTIONS` 为可选命令
- `CONTAINER` 为容器名称，可以通过 `docker images` 查看所有容器来寻找容器名称
- `COMMAND [ARG...]` 为命令部分

```shell
docker exec -it nextcloud /bin/bash
```

上述命令可以进入名称为 `nextcloud` 的容器，并且使用 `-i -t` 命令时，可以看到熟悉的 Linux 命令提示符界面.  

:information_source:如果需要使用 `php occ` 命令，可能需要通过 `www-data` 用户进入，即便虚拟机提示在命令前加入 `sudo -u www-data` 或 `-u www-data` ，但仍然不能执行命令，此时需要对上述命令加入可选参数 `-u` 并指定用户名

```
docker exec -itu www-data nextcloud /bin/bash
```

否则可能会看到提示信息

```shell
root@49f30a23a9b4:/var/www/html# php occ 
Console has to be executed with the user that owns the file config/config.php
Current user: root
Owner of config.php: www-data
Try adding 'sudo -u www-data ' to the beginning of the command (without the single quotes)
If running with 'docker exec' try adding the option '-u www-data' to the docker command (without the single quotes)
```

:information_source: ​这里推荐使用 `docker exec` 而不是 `docker attach` 命令，是因为在使用前者的情况下，在虚拟机内执行 `exit` 命令退出虚拟机时，不会关闭容器，而后者相反

另外需要注意的是，虚拟机内可能没有 vim 相关功能，修改 `config.php` 文件需要从外部进行覆盖，并且覆盖之后需要赋予新的 `config.php` 文件相应权限，否则会无法访问

```shell
docker cp config.php nextcloud:/var/www/html/config/ # 覆盖 config.php 文件
docker exec -it nextcloud bash # 进入容器内部
chmod 777 config.php # 授予 config.php 文件最高权限
```

