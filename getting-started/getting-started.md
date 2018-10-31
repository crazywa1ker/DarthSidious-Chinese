# 前言

**本指南/教程将从以下几个方面展开:**

* 搭建一个虚拟的活动目录(Active Directory)域实验环境
* 凭证重放攻击
* 域权限提升
* Dump系统和域秘钥
* Empire, Bloodhound和ranger等工具的使用
* 实际渗透测试案例

如果你对上面的内容感到不知所措，那么我推荐你先看一下[Windows微指南 ](https://github.com/chryzsh/DarthSidious/tree/fdd707cf9dbbc2faf3cf3dbbcd712b06fceeee87/labs/stuff/miniguide.md)之后再开始阅读[搭建实验环境](https://github.com/chryzsh/DarthSidious/tree/fdd707cf9dbbc2faf3cf3dbbcd712b06fceeee87/labs/labs/building-a-lab.md)。

如果你已经清楚以上的内容并且已经搭建好了一套实验环境，那么我建议你直接看[Network access to Domain Admin](https://github.com/chryzsh/DarthSidious/tree/fdd707cf9dbbc2faf3cf3dbbcd712b06fceeee87/labs/general/network-access-to-domain-admin.md)的内容，去了解攻击Active Directory域的一般方法。

**免责声明**

未经其合法所有者的完全授权，不得在未经授权的环境中使用本书中演示的工具。也不要在懵逼状态下去运行那些你不清楚含义的命令。

**待办事项清单**

* 进一步修改文章
* 对Active Directory的简介
* AD中的Kerberos和身份认证等内容
* 对PowerShell的简介
* 攻击MSSQL Servers的内容
* 客户端攻击
* 域枚举和信息收集
* 本地特权升级
* 交换枚举和攻击
* Sharepoint枚举和攻击
* 针对常见攻击的防御措施
* AD安全的一般性建议

**未来的计划**

* 对Kerberos的攻击和防御 \(黄金、白银票据 攻击等\)
* 委派问题
* 持久性技术
* AD环境中SQL Server凭信滥用问题
* 后门、命令与控制
* AD中林和域的信任
* 攻击检测技术
* Active Directory环境的防御
* [攻击域信任凭据](http://www.harmj0y.net/blog/redteaming/a-guide-to-attacking-domain-trusts/)
* LDAP与非Microsoft产品的集成