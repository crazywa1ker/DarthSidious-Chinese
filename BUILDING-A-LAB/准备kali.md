# 准备一台Kali
您需要一些工具来为后面的实验做准备！创建一个目录然后将这些工具放到里面，不然会很乱。我不会解释这些工具是干啥的以及怎么安装它们，因为不同的工具和程序可能会发生变化。自己谷歌，丰衣足食。

- [Impacket](https://github.com/CoreSecurity/impacket) :python的网络工具包，很有用。

- [Responder](https://github.com/lgandx/Responder):Kali中的网络请求攻击工具。

- [Empire] (https://github.com/EmpireProject/Empire):类似msf，只针对windows，使用powershell。

- [CrackMapExec] (https://github.com/byt3bl33d3r/CrackMapExec):包含SMB签名的工具包，用于横向渗透。
- [DeathStar](https://github.com/byt3bl33d3r/DeathStar) :自动化域渗透工具。

- [BloodHound ](https://github.com/BloodHoundAD/BloodHound):创建一个AD Map，分析DC。GitHub上有预编译二进制文件！

- [PowerSploit](https://github.com/PowerShellMafia/PowerSploit):PowerShell的工具，用于后渗透，一些工具集成在了Empire中。

- [Mimikatz](https://github.com/gentilkiwi/mimikatz):dump凭据。

- [Neo4j](https://neo4j.com/download/) :BloodHound的数据库

其中大部分工具应该可以通过apt提供。如果没有的话，请通过`git clone`并将它们放在`/opt`目录安装。现在，您可能需要安装工具及其依赖项。其中很多是用Python编写的，所以要熟悉pip。确保使用正确的Python版本运行工具和脚本。某些工具是为2.7编写的，有些是为3.x编写的，本教程的后续部分将介绍如何使用这些工具。
