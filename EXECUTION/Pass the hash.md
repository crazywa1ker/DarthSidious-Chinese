## Pass the hash
Windows域渗透的首要目标是获取一个用户权限或一个shell会话。

NTLM hashes**可以**用来进行Pass-The-Hash攻击。

Net-NTLM hashes**不可以**用来进行Pass-The-Hash攻击。

可以使用metasploit的PSExec的模块来获取哈希值。

若果你想批量获取多台设备的哈希，那Metasploit的么smb_login模块就适合你。但是如果使用域控进行尝试，假设它们有一个锁定策略，就有可能导致帐户被锁定在域之外。

Empire还在`credentials/mimikatz/pth`模块中执行pass-the-hash的提供了相关可选项。https://www.powershellempire.com/?page_id=270

###Ranger

Ranger是一个很奈斯的工具，它可以以多种方式与Windows系统交互。

https://github.com/funkandwagnalls/ranger

Rangers这个工具使用命令行进行操作，它能够使用实例化的catapult服务器提供针对Windows系统的功能。只要用户具有一组凭证或哈希散列集(NTLM, LM, LM:NTLM)，他或她就可以访问信任之外的系统。




