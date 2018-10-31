## Windows的哈希
Windows中有几种不同类型的哈希值，它们可能非常混乱。 可以在这两篇文章[《LM, NTLM, Net-NTLMv2, oh my!》](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4)和[《LM Hash and NT Hash
》](http://www.adshotgyan.com/2012/02/lm-hash-and-nt-hash.html)找到一些解释，但首先阅读下面这段内容：

如果Windows的hash没有加盐，那么两组hash将可能产出相同的明文。Windows哈希分为两组--LMhash和NTLMhash。 比如：
`testuser:29418:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71`

内容的构成:  
`username : unique_identifier : LMhash : NThash`

* **LM** - LM哈希用于存储密码。 但要注意在W7及以上版本中LM是禁用的。 但是，当密码少于15个字符的时候，就在内存中启用LM。 这也是为什么管理员帐户的所有建议都是15个字符的原因。 LM已经过时了，它基于MD4，很容易破解。 它之所以依旧采用MD4，是因为Windows域需要较快的解密速度，但这也使得它的安全性变得很差。
* **NT** -  NT哈希根据用户输入的整个密码计算哈希值。 LM哈希将密码拆分为两个7个字符的块，如果有缺省则进行填充。
* **NTLM** - NTLM哈希用于域中主机上的本地身份验证。 如上所示，它是由LM和NT哈希组合而成。
* **NetNTLMv1/2** - 有时候在网络进行身份验证的时候\(比如SMB\)，有时候也会提到NTLMv2，但是请不要把它和NTLM hash搞混了，他俩是不同的概念。

在Windows中，哈希存储在内存中以用于单点登录。 每次用户点击网络共享时，信用凭证都会通过网络传递。 我们可以利用这个特性在传输过程中或在计算机上获取这些凭据。 另一种方法是始终向用户询问凭据，这在Windows环境中很少发生。

在对Windows机器进行身份验证时，NTLM哈希与明文凭证效果是一样的。因此如果你无法获取明文凭据的时候，并不要认为这是什么大问题。 其实，我的意思就是可以通过哈希传递来通过身份验证，而不需要明文密码本身。

破解哈希可以很有趣，而且由于大多数用户密码都很糟糕/不复杂，因此很容易秒破。 想象一下，如果域名的密码重置策略为90天。 但是你在两小时内就破解了用户的凭据，那这对他们来说是一个很大的失误。 如果你可以在两小时甚至几天内破解域管理员的凭证，那么它们也就离玩儿完不远了。 当然，我们可以利用中继来重用凭证，而不需破解哈希。

## Windows中的身份验证

在Windows系统中有许多验证身份的方法。

* **密码** - 也就是密码本身了
* **Hashes** - Windows可以使用哈希进行身份验证。 因此我们可以使用重传hash等攻击来进行身份验证，完全不需要帐户密码。
* **Tokens** - 令牌属于身份验证的概念。 当用户或服务登录系统时，系统会验证一次其身份，并生成一个令牌，将该令牌将传递给该用户/服务并作为其身份。 例如，每当程序打开文件时，系统就不需要验证身份。 这基本上确保了身份验证\(证明用户/服务是他们所说的人)和授权\(确定用户/服务是否可以访问某些资源)之间的清晰分离。
* **Tickets** - 通常指Kerberos票据, 详见下文。

## Kerberos

Kerberos是Windows中基于\_tickets\_的身份验证协议，允许通过非安全网络进行通信的计算机以安全的方式相互提供其身份。 Kerberos建立在对称密钥加密之上，需要受信任的第三方参与，并且可选则在身份验证的某些阶段使用公钥加密。 Kerberos默认使用UDP端口88。

多年来，已有多个Kerberos漏洞被发现：

* [MS14-068](https://www.exploit-db.com/exploits/35474/)

## Windows名称解析

Windows中有一些不同的名称解析协议和名称：

* **FQDN** - 完全限定域名(Fully Qualified Domain Name)
* **WINS** - Windows 网络名称服务(Windows Internet Name Service)
* **NBT-NS** - \(NetBIOS Name Service\) - 通常称为 **NetBIOS**
* **LLMNR** - 链路本地多播名称解析(Link-Local Multicast Name Resolution)
* **WPAD** - Web代理自动发现协议(Web Proxy Auto-Discovery Protocol)

如果名称是**FQDN**，这意味着包括域名的全名，例如`test.lab.local`，它查询hosts文件，然后查询DNS服务器以进行名称解析。

如果名称是非限定名称，如`\\ fileshare`，则尝试使用以下名称解析来查找该文件共享位置：

1. **LLMNR** - 使用多播来为相邻计算机的名称执行名称解析，而无需DNS服务器。
2. **NetBIOS** - 查询WINS服务器以获取解决方案（如果存在）。 如果没有，它使用广播来解析邻近计算机的名称。

由于FQDN查找在文件共享时并不常用，并且默认情况下未启用，因此它会检查LLMNR，然后检查NetBIOS。 在企业界，DNS服务器可用于查找资源，在家庭环境中它不太能行，因此如果您想在两台主机之间共享内容，LLMNR和NetBIOS则成为不二之选。 但是，用户通常不会在资源管理器的地址字段中键入`share.hacklab.net`，因此名称解析将由LLMNR和NetBIOS来完成。