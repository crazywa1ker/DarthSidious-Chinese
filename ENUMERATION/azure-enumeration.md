# 枚举Azure

## 常见配置错误

_或者说Microsoft那些默认值带来的安全风险_

默认情况下，Azure对组织中的每个用户开放。这意味着，例如Office 365的客户很可能没有设置条件访问策略来阻止用户登录 [portal.azure.com](http://portal.azure.com/) 并检索每个用户，角色和组。因此他们不这样做，他们就不会清楚地知道用户在那里做什么。但那又为什么要询问域控制器并检测是否可以从Azure获得所需内容？下面列出了一些很有用的Powershell工具。

## 资料、工具、程序

### **一些很有用的文章**

* [https://www.blackhillsinfosec.com/red-teaming-microsoft-part-1-active-directory-leaks-via-azure/](https://www.blackhillsinfosec.com/red-teaming-microsoft-part-1-active-directory-leaks-via-azure/)
* [https://blog.netspi.com/enumerating-azure-services/](https://blog.netspi.com/enumerating-azure-services/)
* [https://blog.netspi.com/get-azurepasswords](https://blog.netspi.com/get-azurepasswords/)

### **工具**

* [https://github.com/mwrlabs/Azurite](https://github.com/mwrlabs/Azurite) - Azurite 被开发用来帮助帮助渗透测试人员和审计人员在Microsoft Azure公共云环境中进行枚举和侦察活动。
* [https://github.com/nccgroup/azucar](https://github.com/nccgroup/azucar) - AZUCAR自动收集各种配置的数据和分析，并分析与特定订阅相关的所有数据，以确定安全风险。
* [https://github.com/NetSPI/MicroBurst](https://github.com/NetSPI/MicroBurst) - MicroBurst 支持Azure服务发现，缺陷配置审计和发送攻击行为（例如凭证转储）功能。它旨在用于针对Azure的渗透测试期间。 
* [https://github.com/chrismaddalena/SharpCloud](https://github.com/chrismaddalena/SharpCloud) - SharpCloud是一个用于查找与Amazon Web Services, Microsoft Azure, and Google Compute有关的凭证文件的C\#小工具。

### **程序**

一个小代码块，列举了一些枚举Azure AD的常用过程。

```text
### 枚举 Azure AD 

#使用Powershell连接到Azure AD
install-module azuread
import-module azuread
get-module azuread
connect-azuread


# 列举 users 的 role global admins信息# 注意 role =! group
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'Company Administrator'}
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId

# 列出所有的 groups 
Get-AzureADGroup
# 使用filter过滤袋一些信息的例子
Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

# 获取 Azure AD policy
Get-AzureADPolicy

# 获取 Azure AD roles 的一些例子
Get-AzureADDirectoryRole
Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'Security Reader'}
Get-AzureADDirectoryRoleTemplate

# 获取 Azure AD SPNs
Get-AzureADServicePrincipal

# 使用 Azure CLI (this is not powershell) 进行登录
az login --allow-no-subscriptions

# 获取成员列表 using Azure CLI
az ad group member list --output=json --query='[].{Created:createdDateTime,UPN:userPrincipalName,Name:displayName,Title:jobTitle,Department:department,Email:mail,UserId:mailNickname,Phone:telephoneNumber,Mobile:mobile,Enabled:accountEnabled}' --group='Company Administrators'

# 获取用户列表
az ad user list --output=json --query='[].{Created:createdDateTime,UPN:userPrincipalName,Name:displayName,Title:jobTitle,Department:department,Email:mail,UserId:mailNickname,Phone:telephoneNumber,Mobile:mobile,Enabled:accountEnabled}' --upn='username@domain.com'

# 用于获取 users / roles 数组的PS脚本
$roleUsers = @() 
$roles=Get-AzureADDirectoryRole
 
ForEach($role in $roles) {
  $users=Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
  ForEach($user in $users) {
    write-host $role.DisplayName,$user.DisplayName
    $obj = New-Object PSCustomObject
    $obj | Add-Member -type NoteProperty -name RoleName -value ""
    $obj | Add-Member -type NoteProperty -name UserDisplayName -value ""
    $obj | Add-Member -type NoteProperty -name IsAdSynced -value false
    $obj.RoleName=$role.DisplayName
    $obj.UserDisplayName=$user.DisplayName
    $obj.IsAdSynced=$user.DirSyncEnabled -eq $true
    $roleUsers+=$obj
  }
}
$roleUsers


# 使用Microburst进行枚举
https://github.com/NetSPI/MicroBurst

Import-Module .\MicroBurst.psm1

# 匿名枚举
Invoke-EnumerateAzureBlobs -Base company
Invoke-EnumerateAzureSubDomains -base company -verbose

# 身份验证枚举
Get-AzureDomainInfo -folder MicroBurst -VerboseGet-MSOLDomainInfo
Get-MSOLDomainInfo
```


