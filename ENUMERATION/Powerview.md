# Powerview

Powerview 是PowerSploit工具集中用于域枚举的一个特别好的工具。几乎所有的功能在Empire中也都有。尽管每个功能都是可以直接看懂的并且你需要自己去探索。我仍然会在这提供一些提示。我强烈建议阅读这类基于Powershell的黑客工具的源码，因为有大量的提示和样例和他们绑在一起。

Harmj0y 在这个 Github 上也有一些技巧和窍门。
<https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993>

---

## User functions

`Invoke-EnumerateLocalAdmin`  枚举域中所有计算机上的本地管理员组的成员。

## Groupfunctions

`Get-NetLocalGroup -ComputerName MX01 -GroupName "Remote Management Users"` 获得所有可以在特定的计算机上使用WinRM的用户。

`Get-NetLocalGroup -ComputerName MX01 -GroupName "Remote Desktop Users"` 获得可以远程桌面(RDP)连接到一台特定的计算机上的用户。

## GPO functions

`Get-NetGPOGroup -ResolveMemberSIDs`  获得在域中所有在目标机器中设置 "Restricted Groups" 的GPO，并且解析出所有组成员和用户成员的SID。

`Find-GPOLocation -UserName testuser -LocalGroup RDP` 选择一个用户或组， 并通过GPO枚举检查该用户可以访问哪些地方。这样我们就可以看到testuser可以用RDP访问到哪些主机。