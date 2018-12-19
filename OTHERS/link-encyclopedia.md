# 链接 列表

## Microsoft
### Powershell
* [Powershell 101](https://hkh4cks.com/blog/2018/01/01/powershell-101/)
* [一个月起步学习Powershell](https://www.youtube.com/playlist?list=PL6D474E721138865A)
* [p3nt4/PowerShdll](https://github.com/p3nt4/PowerShdll) - 使用dll运行Powershell,这样就不需要使用Powershell.exe
* [nullbind/Powershellery](https://github.com/nullbind/Powershellery) - GetSPN 和一些其他的东西。

### Empire
* [Empire 101](http://www.powershellempire.com/?page_id=110) - Empire 官方文档

### Powerview
* [Powerview repository](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon) - 包含一些文档和如何使用 Powerview
* [PowerView-3.0-tricks.ps1](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993) - 来自 HarmJ0y 大牛的一些关于 PowerView 的技巧和窍门。

### Bloodhound
* [Bloodhound node info](https://github.com/BloodHoundAD/BloodHound/wiki/Users) - 关于Bloodhound 节点信息的解释。
* [Lay of the land with bloodhound](http://threat.tevora.com/lay-of-the-land-with-bloodhound/) - 通用的BloodHound的使用手册。

### Mimikatz
* [Lazykats](https://github.com/bhdresh/lazykatz) - 一些绕过杀毒软件的Mimikatz
* [Direct link to Invoke-Mimikatz.ps1​](https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Exfiltration/Invoke-Mimikatz.ps1)
* [Auto dumping domain credentials​](https://blog.netspi.com/auto-dumping-domain-credentials-using-spns-powershell-remoting-and-mimikatz/)
* [eladshamir/Internal-Monologue](https://github.com/eladshamir/Internal-Monologue) - nternal Monologue Attack: 在不触碰LSASS的情况下收集NTLM哈希。

### Enumeration
* [Invoke-Portscan.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/262a260865d408808ab332f972d410d3b861eff1/Recon/Invoke-Portscan.ps1) - Invoke-Portscan 是一个从Powershell中类似于nmap的能扫描端口的PowerSpolit模块。
* [Walking back local admins](http://www.sixdub.net/?p=591) - 在 AD 中寻找本地管理员。

### Kerberos
* [HarmJ0y - roasting-as-reps](http://www.harmj0y.net/blog/activedirectory/roasting-as-reps/) - 关于Kerberos预认证的文章。
* [HarmJ0y/ASREPRoast](https://github.com/HarmJ0y/ASREPRoast) - 在未开启 Kerberos 预认证的情况下，从 KRB5 AS-RE 响应中寻找可以破解的hash。


### Tunneling
* [SShuttle](http://sshuttle.readthedocs.io/en/stable/) - SShuttle 可以建立起一个像vpn一样的ssh隧道。

### Command and control (C2)
* [SANS Pentest Blog](https://pen-testing.sans.org/blog/2017/12/10/putting-my-zero-cents-in-using-the-free-tier-on-amazon-web-services-ec2) - 使用亚马逊 AWS EC2 进行控制。
* [lukebaggett/dnscat2-powershell](https://github.com/lukebaggett/dnscat2-powershell) Powershell实现的dnscat2客户端。
* [C2 with dnscat2 and powershell](https://www.blackhillsinfosec.com/powershell-dns-command-control-with-dnscat2-powershell/) - dnscat2可以和Powershell一起使用，通过dns来隐藏远控行为。
* [DNS tunneling](https://pentest.blog/data-exfiltration-tunneling-attacks-against-corporate-network/) - dns隧道是如何工作的。

### Exploit
* [SharpShooter](https://github.com/mdsecactivebreach/SharpShooter) - SharpShooter 可以使用多种形式创建payloads，比如HTA, JS和VBS。
* [DCShadow](https://blog.alsid.eu/dcshadow-explained-4510f52fc19d) - DCShadow, 一种创建流氓域控制器的攻击技术。
*
### Mail
* [Ruler](https://github.com/sensepost/ruler) - Ruler 可以和远程交换服务器通信。

### Breaking out of locked down environments
* [Breaking Out of Citrix and other Restricted Desktop Environments​](https://www.pentestpartners.com/security-blog/breaking-out-of-citrix-and-other-restricted-desktop-environments/)
* [Applocker Case study](https://oddvar.moe/2017/12/21/applocker-case-study-how-insecure-is-it-really-part-2/) - 使用高级技术突破 Applocker。
* [Bypass Applocker](https://github.com/api0cradle/UltimateAppLockerByPassList) - 最著名的Applocker绕过技术列表。
* [Babushka Dolls or How To Bypass Application Whitelisting and Constrained Powershell​](https://improsec.com/blog/babushka-dolls-or-how-to-bypass-application-whitelisting-and-constrained-powershell)

### 防御
* [MS - Securing privileged access](https://docs.microsoft.com/en-us/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) - 在AD中进行安全访问管理的一些参考材料。
* [​MS - What is AD Red Forest](https://social.technet.microsoft.com/wiki/contents/articles/37509.what-is-active-directory-red-forest-design.aspx) - Red forest design 是在考虑安全的情况下设计一个可管理的AD环境。
* [Managing Applocker with Powershell​](https://4sysops.com/archives/managing-applocker-with-powershell/)
* [SANS - Finding Empire C2 activity​](https://www.sans.org/reading-room/whitepapers/detection/disrupting-empire-identifying-powershell-empire-command-control-activity-38315)

### 实验环境搭建
* [The Eye](https://the-eye.eu/public/MSDN/) - 所有系统的官方ISO镜像。
* [Automatedlab/Automatedlab](https://github.com/AutomatedLab/AutomatedLab) - Automatedlab 是一个使用Powershell搭建实验环境的项目。
* [Building a lab with ESXI and Vagrant](https://github.com/chryzsh/DarthSidious/tree/fdd707cf9dbbc2faf3cf3dbbcd712b06fceeee87/building-a-lab-with-esxi-and-vagrant.md) - 本书中关于使用ESXi搭建实验环境的长文。
* [Mini lab](https://github.com/chryzsh/DarthSidious/tree/fdd707cf9dbbc2faf3cf3dbbcd712b06fceeee87/creating.md) - 本书中关于创建小型的实验环境的短文，此环境可用于练习类似于应急响应的事情。

### 其他
* [OSCP Survival Guide archived](http://web.archive.org/web/20171014213457/https://github.com/frizb/OSCP-Survival-Guide) - 一大堆关于枚举和利用漏洞的命令。