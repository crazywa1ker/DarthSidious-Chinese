##Responder with NTLM relay and Empire
byt3bl33d3r针对本次介绍的这种攻击方式写过一个详细的指南。直通车[byt3bl33d3r](https://byt3bl33d3r.github.io/practical-guide-to-ntlm-relaying-in-2017-aka-getting-a-foothold-in-under-5-minutes.html) 

NetNTLMv2是微软的一种交换和响应协议。它使用了用户的哈希散列来进行服务器进行身份验证，并用中继散列来与服务器的交换数据。您只需将收集到的NetNTLMv2散列传递到一组主机，如果该主机的用户具有管理员的权限。那么你就能执行有效载荷，并且反弹到一个shell。

在windows AD环境中，10个工作站中有9个共享相同的本地管理员密码。您可能非常幸运，主机和服务器也共享相同的本地管理员密码。这时，它就变成了域管理员登录的发现任务。

在此之前，我们必须在域中生成一个可能受到攻击的主机列表。这是通过禁用smb签名来表示的，这在大多数Windows操作系统中是默认的，服务器除外。

从CrackMapExec包执行此操作:

```
cme smb <ip> --gen-relay-list targets.txt
```

这条命令将生成禁用了smb签名的主机列表。如果是启用了smb签名的Windows服务器，那我们不能转发到那个上面去。同理也不能将从主机发出的请求传递给主机。

###Responder
​https://www.sternsecurity.com/blog/local-network-attacks-llmnr-and-nbt-ns-poisoning​
​
​Responder并不会接收FQDN的查询，但是它会接收NetBIOS和LLMNR的请求，因为当Windows验证文件共享之类的东西时，默认为使用NetBIOS进行查询。FQDN的查询是使用NetNTLMv2哈希的。
​
​Responder会获取到这些NetNTLMv2哈希。虽然不能直接使用，但是可以暴力破解出明文，再或者也可以把它们传递给其他机器。本文仅说明如何运行Responder和获取哈希。
​
​
​​####Responder使用

```​
​Responder -I eth0 -wrf
```

-I 参数是加载自己的网卡，-wrf视情况选择，值得一提的是-f参数是用来获取系统版本的，很常用。其他参数的细节可以使用-h逐一查看。




