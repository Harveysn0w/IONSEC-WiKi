# Pikachu靶场通关之越权漏洞

## 1、越权漏洞概述

![image-20210103183831007](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103183831007.png)

```
如果使用A用户的权限去操作B用户的数据，A的权限小于B的权限，如果能够成功操作，则称之为越权操作。 越权漏洞形成的原因是后台使用了 不合理的权限校验规则导致的。
一般越权漏洞容易出现在权限页面（需要登录的页面）增、删、改、查的的地方，当用户对权限页面内的信息进行这些操作时，后台需要对 对当前用户的权限进行校验，看其是否具备操作的权限，从而给出响应，而如果校验的规则过于简单则容易出现越权漏洞。
因此，在在权限管理中应该遵守：
1.使用最小权限原则对用户进行赋权;
2.使用合理（严格）的权限校验规则;
3.使用后台登录态作为条件进行权限判断,别动不动就瞎用前端传进来的条件;
你可以通过“Over permission”对应的测试栏目，来进一步的了解该漏洞。
```

## 2、平行&水平越权漏洞

A和B属于同一个级别的用户，如A登录后可以查看自己的信息，他发现提交查看自己信息的请求时候有个username参数，修改username参数可以查看B的个人信息，这就是水平越权了。

![image-20210103184300032](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103184300032.png)

这里我们使用lucy的账号先进行登录~

![image-20210103184237737](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103184237737.png)

```
http://127.0.0.1/pikachu/vul/overpermission/op1/op1_mem.php?username=lucy&submit=%E7%82%B9%E5%87%BB%E6%9F%A5%E7%9C%8B%E4%B8%AA%E4%BA%BA%E4%BF%A1%E6%81%AF#
```

我们来观察这个URL，我们是不是可以将lucy更改为其他用户呢？

我们将lucy更改为lili，成功访问~这里就存在水平越权漏洞了。。。

![image-20210103184432403](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103184432403.png)

成功显示了lili的个人信息，水平越权漏洞验证成功。

![image-20210103184629045](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103184629045.png)

我们看到后端代码 并没有进行校验，因此导致了越权漏洞。

## 3、垂直越权漏洞

垂直越权一般发生在普通用户越权可以操作管理员权限的情况。这里的情况是管理员可以查看和新增用户，普通用户只可以查看用户。这里验证过程为：

1. 登录管理员帐号，新增用户，提交请求抓包。
2. 退出管理员帐号，重放新增用户请求，发现不能成功新增用户。
3. 登录普通用户，发现没有新增用户权限，但替换掉1中抓包的请求cookie部分，重放新增用户请求
4. 用户新增成功，垂直越权漏洞验证成功。

根据提示，猜测应该是pikacu越权admin。

![image-20210103184936296](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103184936296.png)

1.pikachu登陆，发现自己只有查看权限

![image-20210103185059119](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103185059119.png)

2.admin管理员登录，有添加用户的权限

![image-20210103185147566](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103185147566.png)

下面我们用admin管理员用户新建一个用户，抓包。发送到重放模块，然后停止截包

![image-20210103191038634](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103191038634.png)

我们可以看到成功的新建了一个用户

![image-20210103191207785](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103191207785.png)

下面admin退出登陆，pikachu登陆系统，目的是为了获取pikachu的cookie，保持截断数据包

![image-20210103191413063](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103191413063.png)

```
pikachu的cookie： PHPSESSID=n2r6tksdpncjd2smc8u63l9kon
admin的Cookie: PHPSESSID=g09d1602c8dc550eke5uapmg2v
```

下面来到重放模块（1）把cookie换成pikachu的（2）修改一下新建用户的信息

![image-20210103191559398](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103191559398.png)

发送~代理模块停止截包~pikachu登陆成功，会看到自己添加的用户sss

![image-20210103192102811](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103192102811.png)

下面我们可以看一下后台的代码-首先是登录页面

![image-20210103192316356](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103192316356.png)

判断是否登录，登录了才有权限~

![image-20210103192410788](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103192410788.png)

这里登录态的验证只是看了用户名密码，并没有判断登录的权限。

![image-20210103192521671](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210103192521671.png)



