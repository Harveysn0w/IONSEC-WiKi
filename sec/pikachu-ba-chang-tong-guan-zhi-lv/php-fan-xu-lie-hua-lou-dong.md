# PHP反序列化漏洞

## 1、PHP反序列化漏洞概述

在理解这个漏洞前,你需要先搞清楚php中serialize\(\)，unserialize\(\)这两个函数。

**序列化serialize\(\)** 序列化说通俗点就是把一个对象变成可以传输的字符串,比如下面是一个对象:

```php
    class S{
        public $test="pikachu";
    }
    $s=new S(); //创建一个对象
    serialize($s); //把这个对象s进行序列化
    序列化后得到的结果是这个样子的: O:1:"S":1:{s:4:"test";s:7:"pikachu";}
        O:代表object
        1:代表对象名字长度为一个字符
        S:对象的名称
        1:代表对象里面有一个变量
        s:数据类型
        4:变量名称的长度
        test:变量名称
        s:数据类型
        7:变量值的长度
        pikachu:变量值
```

**反序列化unserialize\(\)**

就是把被序列化的字符串还原为对象,然后在接下来的代码中继续使用。

```php
    $u=unserialize("O:1:"S":1:{s:4:"test";s:7:"pikachu";}");
    echo $u->test; //得到的结果为pikachu
```

**PHP 序列化与反序列化-案例ser.php：**

```php
class A{
    public $test="pikachu";
    }
$s=new S(); //创建一个对象
serialize($s); //把这个对象s进行序列化
echo $serdata;//得到序列化的值
echo '<br>';
$u=unserialize("O:1:"S":1:{s:4:"test";s:7:"pikachu";}");
echo $u->test; //反序列化得到的结果为pikachu
```

序列化和反序列化本身没有问题,但是如果反序列化的内容是用户可以控制的,且后台不正当的使用了PHP中的魔法函数,就会导致安全问题

```php
        常见的几个魔法函数:
        __construct()当一个对象创建时被调用

        __destruct()当一个对象销毁时被调用

        __toString()当一个对象被当作一个字符串使用

        __sleep() 在对象在被序列化之前运行

        __wakeup将在序列化之后立即被调用

        漏洞举例:

        class S{
            var $test = "pikachu";
            function __destruct(){
                echo $this->test;
            }
        }
        $s = $_GET['test'];
        @$unser = unserialize($a);
//这里的使用了php魔法函数所以导致反序列化漏洞
        payload: O:1:"S":1:{s:4:"test";s:29:"<script>alert('xss')</script>";}
```

这里的功能是输入一个序列化数据，源码审计此页面是根据序列化的数据创建S对象时执行了echo，返回对象里test变量的值。如构造正常的值如

```php
O:1:"S":1:{s:4:"test";s:7:"pikachu";}
```

提交后会返回pikachu，但如果提交构造的payload如

```php
O:1:"S":1:{s:4:"test";s:29:"<script>alert('xss')</script>";}
```

则S执行destruct方法时就会echo js脚本，在前端实现xss的效果了。

那么如何构造一个正确的payload的呢？我们在php里面写一个类然后定义一个变量，把变量的名称写成一段恶意的js代码然后new一下对他进行序列化。得到的序列化的内容也就是我们的payload

```text
class S{
    var $test = "<script>alert('xss')</script>";
    }
echo '<br>';
$a=new S(); //创建一个对象
echo $serialize(a);
```

序列化漏洞很难通过黑盒测试发现，一般发掘反序列化漏洞主要还是通过源码审计。

## 2、靶场实战演练

我们在靶场中输入payload即已经定义好的反序列化语句。

![image-20210104101134325](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104101134325.png)

![image-20210104101324177](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104101324177.png)

这里就是提交一个序列化的内容，然而序列化中的内容就是恶意js代码，把他提交后台，后台会进行反序列化，然后通过魔法方法echo到了前端。这样就是通过反序列化的一个接口造成了xss的执行。

## 3、代码审计

在后端代码中我们可以看到后端使用了一个php魔法方法

![image-20210104112607268](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104112607268.png)

这边就是把接收到的数据做一个反序列化。

![image-20210104112659119](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104112659119.png)

