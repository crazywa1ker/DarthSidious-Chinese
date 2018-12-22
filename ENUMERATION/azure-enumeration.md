# Azure 枚举

枚举 Azure 服务。

---

## 常见错误配置

或者说，微软的默认参考配置。

Azure 默认对组织中的所有用户开放。这意味着，例如有一个 Office 365 的客户端很可能没有设置条件访问策略来防止用户登录到 <portal.azure.com> 检索每个用户、角色和组。奇怪的是，如果他们配置了访问策略，他们也不会密切监视用户在那里都干了啥。那么，如果可以从Azure中直接获得所需的内容，为什么要问内部域控制器？况且还可能被检测到。下面是用Powershell的骚操作。

## 过程和工具

可能有用的一些资料

* <https://www.blackhillsinfosec.com/red-teaming-microsoft-part-1-active-directory-leaks-via-azure/>
* <https://blog.netspi.com/enumerating-azure-services/>
* <https://blog.netspi.com/get-azurepasswords/>

## 工具

* <https://github.com/mwrlabs/Azurite> - Azurite 用于帮助渗透测试人员和审计人员枚举和探测 Microsof Azure公共云环境。
* <https://github.com/nccgroup/azucar> - Azucar 能够自动收集各种配置参数，并分析与一些特定订阅相关的所有数据，从而确定安全风险。
* <https://github.com/NetSPI/MicroBurst> - MicroBurst 有一些脚本和函数，具体功能包括 Azure服务发现、脆弱配置的审计 和 一些后期利用，比如dump 证书等。
* <https://github.com/chrismaddalena/SharpCloud> - SharpCloud 是一个简单的C#工具，用于检查与 Amazon Web Services、Microsoft Azure和 Google Compute 相关的凭证文件。

## 过程

一小段代码块，枚举 Azure AD 的一般过程。

```powershell
### Azure AD enumeration
​
#Connect to Azure AD using Powershell
install-module azuread
import-module azuread
get-module azuread
connect-azuread
​
# Get list of users with role global admins# Note that role =! group
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'Company Administrator'}
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
​
# Get all groups and an example using filter
Get-AzureADGroup
Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"
​
# Get Azure AD policy
Get-AzureADPolicy
​
# Get Azure AD roles with some examples
Get-AzureADDirectoryRole
Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'Security Reader'}
Get-AzureADDirectoryRoleTemplate
​
#Get Azure AD SPNs
Get-AzureADServicePrincipal
​
# Log in using Azure CLI (this is not powershell)
az login --allow-no-subscriptions
​
#Get member list using Azure CLI
az ad group member list --output=json --query='[].{Created:createdDateTime,UPN:userPrincipalName,Name:displayName,Title:jobTitle,Department:department,Email:mail,UserId:mailNickname,Phone:telephoneNumber,Mobile:mobile,Enabled:accountEnabled}' --group='Company Administrators'
​
#Get user list
az ad user list --output=json --query='[].{Created:createdDateTime,UPN:userPrincipalName,Name:displayName,Title:jobTitle,Department:department,Email:mail,UserId:mailNickname,Phone:telephoneNumber,Mobile:mobile,Enabled:accountEnabled}' --upn='username@domain.com'
​
#PS script to get array of users / roles
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
​
### Enumeration using Microburst
https://github.com/NetSPI/MicroBurst
​
Import-Module .\MicroBurst.psm1
​
#Anonymous enumeration
Invoke-EnumerateAzureBlobs -Base company
Invoke-EnumerateAzureSubDomains -base company -verbose
​
#Authencticated enumeration
Get-AzureDomainInfo -folder MicroBurst -VerboseGet-MSOLDomainInfo
Get-MSOLDomainInfo
```
