---
description: CVE-2017-8291
---

# Python PIL 远程命令执行漏洞

## 【原理】

Python中处理图片的模块PIL（Pillow），因为其内部调用了GhostScript而受到GhostButt漏洞（CVE-2017-8291）的影响，造成远程命令执行漏洞。

## 【工具】

* Python
* Docker

## 【步骤】

1.点击启动场景按钮，等待场景启动完成后，通过靶标接入协议进入漏洞标靶

2.运行环境：

```text
➜  cd /Users/harveysn0w/Documents/CVE-2017-8291/env
➜  env ls
app.py             docker-compose.yml
➜  docker-compose up -d
Creating network "env_default" with the default driver
Pulling web (vulhub/ghostscript:9.21-with-flask)...
9.21-with-flask: Pulling from vulhub/ghostscript
7919f5b7d602: Pull complete
c6c44661db59: Pull complete
9c50e2687d10: Pull complete
27869d33eb7b: Pull complete
e228e686e2b9: Pull complete
a3ed95caeb02: Pull complete
Digest: sha256:4e42c01b5f0670f845cdc4c2a76a1f6baa965d58184e2e040a5c635fd7059654
Status: Downloaded newer image for vulhub/ghostscript:9.21-with-flask
Creating env_web_1 ... done
➜  env
```

3.运行后，访问`http://your-ip:8000/`即可看到一个上传页面。

```text
127.0.0.1:8000
```

![image-20210209112421749](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210209112421749.png)

正常功能是我们上传一个PNG文件，后端调用PIL加载图片，输出长宽。但我们可以将可执行命令EPS文件后缀改成PNG进行上传，因为后端是根据文件头来判断图片类型，所以无视后缀检查。

![image-20210209120211399](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210209120211399.png)

![image-20210209112640075](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210209112640075.png)

比如 **poc.png** 我们上传此POC文件。

![image-20210209112933783](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210209112933783.png)

我们上传之后，返回如下结果。

![image-20210209113026826](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210209113026826.png)

即可执行`touch /tmp/aaaaa`。将POC中的命令改为反弹命令，即可获得shell。先看一下靶机的ID接着获得shell。

![image-20210209121834394](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210209121834394.png)

![image-20210209121630284](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210209121630284.png)

```text
➜  docker ps -a
CONTAINER ID         
6189b0b04ba5          
➜  docker exec -it 6189b0b04ba5 bash
root@6189b0b04ba5:/usr/src# pwd
/usr/src
root@6189b0b04ba5:/usr/src# ls /tmp
aaaaa  poc.png
root@6189b0b04ba5:/usr/src#
```

## exp&poc

[文件下载地址](https://gitee.com/Harveysn0w/mac-note_img/raw/master/poc.png)

## 关闭镜像（每次用完后关闭）

```text
docker-compose down
```

