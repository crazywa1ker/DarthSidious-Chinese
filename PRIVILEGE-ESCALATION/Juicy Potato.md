# Juicy Potato
ps:你要是翻译成好吃的土豆的的话，emmm，这人喜欢吃土豆，起的啥名？
我就翻译成可爱土豆了，咋滴。

[juicy-potato利用金票据提权](https://ohpe.github.io/juicy-potato/)

可爱土豆是`RottenPotato`漏洞利用的核武器级别的版本，利用微软处理令牌方式的原理，从而实现提权。

## 工作原理
我对Windows也并不是了解得有多深，但我就是想知道这个漏洞的原理。
`CLSID`是标识COM类对象的全局唯一标识符。这个漏洞允许我们从`session 0`中的服务帐户升级到SYSTEM权限。

## 怎么用呢？
我使用的是`Windows 10 Enterprise 1709`，但操作系统问题不大。但需要一个shell作为服务帐户。出于演示的目的呢，我使用了`nt authority\local service`

`SeAssignPrimaryTokenPrivilege SeImpersonatePrivilege`这个具有大多数服务账户拥有的特性。

尝试用`Microsoft Sysinternals`中的`psexec`打开一个shell作为服务帐户，如下面的截图所示。

```
PsExec64.exe -i -u "nt authority\local service" cmd.exe
```

然后我们从这里选择一个[CLSID](https://ohpe.it/juicy-potato/CLSID/) 。
说明：许多CLSID属于`LOGGED-IN-USER`，所以如果你选择用这玩意去登陆域管理员，你基本上可以直接升级到DA。但是，它只会获得第一个`session 1`的用户。找到预测的用户，然后进一步测试。无论哪种方式，SYSTEM权限是我们继续往下的前提。

现在我们通过指定1337的COM端口通过下面两种技术:

```
CreateProcessWithTokenW
CreateProcessAsUsernt 
```
去执行进程cmd.exe，
然后利用成功，  

```
authority\system
```

[](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LBXJ8RJm7fk-rUVqjK5%2F-LK15IvAQgIYShDQ1y-I%2F-LK13cS0pHnAzoMad3__%2Fimage.png?alt=media&token=4f9ec76b-00b4-4cca-9d5f-71bc1c6cbf7b)

## 修复
这是由于服务帐户在启用[kerberos委派](https://technet.microsoft.com/en-us/library/cc995228.aspx)时需要模拟用户的方式。
根据某些的说法，实际的解决方案是直接保护在帐户下运行的敏感帐户和应用程序。
也就是
```
* SERVICE
```


