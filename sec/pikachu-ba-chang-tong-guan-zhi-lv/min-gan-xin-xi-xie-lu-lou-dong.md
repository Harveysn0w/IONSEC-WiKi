# 敏感信息泄露漏洞

## 1、敏感信息泄露漏洞概述

敏感信息泄漏，是指人们把不该公开的信息，给放入到公开的信息库中。造成敏感信息泄露。只要能被黑客看到并对黑客的入侵有帮助的信息都可以属于敏感信息。

```text
敏感信息泄露概述
由于后台人员的疏忽或者不当的设计，导致不应该被前端用户看到的数据被轻易的访问到。 比如：
---通过访问url下的目录，可以直接列出目录下的文件列表;
---输入错误的url参数后报错信息里面包含操作系统、中间件、开发语言的版本或其他信息;
---前端的源码（html,css,js）里面包含了敏感信息，比如后台登录地址、内网接口信息、甚至账号密码等;
类似以上这些情况，我们成为敏感信息泄露。敏感信息泄露虽然一直被评为危害比较低的漏洞，但这些敏感信息往往给攻击着实施进一步的攻击提供很大的帮助,甚至“离谱”的敏感信息泄露也会直接造成严重的损失。 因此,在web应用的开发上，除了要进行安全的代码编写，也需要注意对敏感信息的合理处理。
你可以通过“i can see your abc”对应的测试栏目，来进一步的了解该漏洞。
```

## 2、实战演练

打开靶场，我们发现提示说泄露了好多东西~

![image-20210104092056204](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104092056204.png)

那么我们就来查看一下网页源代码吧~果然在此找到了测试的账号密码。

![image-20210104092222750](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104092222750.png)

成功登录~

![image-20210104092341606](https://gitee.com/Harveysn0w/win-note_img/raw/master/image-20210104092341606.png)

这里登录页面源码中可以看到注释掉的测试用户名和密码，lili/123456。lili登录之后的cookie字段的比较明显的pw字段弱加密, 删除url路径上的文件名可以看到同级目录下的文件和中间件信息，这些都属于敏感信息泄漏。

