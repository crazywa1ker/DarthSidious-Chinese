# Cuckoo 恶意软件分析环境
> 欲速则不达，konw it，then hack it。

译者注：Cuckoo Sandbox是领先的开源自动恶意软件分析系统
https://cuckoosandbox.org/ 


Rastamouse的这篇文章给我很大的启发：https://rastamouse.me/2017/05/playing-with-cuckoo/
我打算弄一个一样的环境。
可能超出了本书的范围，如果您想开发自己的payload和工具，则必须在将它们放入生产环境之前对其进行测试。

我用一些方法来解决这个问题。所以呢，在这个wiki里面，我尽量去解决一些在设置时不能很好地工作的问题，使其尽可能顺利。其结果与RASTA的实验环境大致相同，如果您需要对此进行可视化，可以参考他的数据。

如果中途有啥问题，推特上找我，[@chryzsh](https://twitter.com/chryzsh)

Rastamouse在他的文章中说过，VMWare在物理主机上是必需的，因为要为恶意软件的沙盒安装64位虚拟机，我们需要支持VT-x/AMD-V。VMWare Workstation / Player没有此限制。

![](https://raw.githubusercontent.com/evilwing/wing-images/master/img/20181102214735.png)

## 设置VMWare

在VMWare workstation中，转到编辑 - >虚拟网络编辑器，将VMnet1（仅限主机）IP子网更改为192.168.45.0/24，并启用DHCP。

我们有时必须更改网络设置，即使在VM运行时也是一样。如果您这样做，请记住在Ubuntu中重新启动网络服务，以便更新配置和接口。
```
sudo /etc/init.d/networking restart
```
## 配置Cuckoo主机

我按照Rasta的wizdom给了Ubuntu服务器以下规格

- 8个CPU

- Enable Virtualize Intel VT-x/EPT or AMD-V/RVI

- 4GB RAM

- 100GB硬盘

- NIC 1：桥接（稍后我们将其更改为NAT）

- NIC 2：仅主机模式

在VMWare中安装Ubuntu 16.04。将用户名设置为cuckoo。

## 安装软件

```
cd~
sudo apt update
sudo apt install python python -pip python-dev libffi-dev libssl-dev python-virtualenv python-setuptools libjpeg-dev zlib1g-dev swig mongodb python-pip virtualbox tcpdump apparmor-utils
```

安装一些其他工具


```
sudo apt install git vim cifs-utils smbclient terminator unzip xfce4 firefox
```
你喜欢的话，现在可以使用XFCE启动到图形用户环境，并从那里完成其余的设置。
```
startx
```

其余设置：
```
sudo aa-disable /usr/sbin/tcpdump
sudo setcap cap_net_raw,cap_net_admin=eip /usr/sbin/tcpdump
pip install -U pip pycrypto distorm3
git clone https://github.com/volatilityfoundation/volatility
cd ~/volatility/
sudo python setup.py install
cd ~/
virtualenv cuckoo
. cuckoo/bin/activate
pip install -U yara-python==3.6.3 cuckoo
cuckoo
exit
```
安装现在应该完成，你已经验证了Cuckoo正在运行。

继续为Virtualbox设置仅主机模式的网络。通过执行以下操作添加新的仅主机网络模式。

```
转到Virtualbox  ->文件 ->首选项 ->网络 ->仅限主机网络 ->按绿色小+图标。
单击其下方的图标，确保IP子网设置为192.168.56.0/24并禁用DHCP。
```
在Ubuntu中重新启动网络服务并验证是否已添加新接口。
```
ip addr
```
## 将ISO从Windows转移到Ubuntu

我在获取文件时遇到了一些小麻烦，因为虚拟机之间的文件共享有时可能有些混乱。所以我更喜欢使用SMB进行转移。您应该在Windows主机上设置共享

使用SMB连接到您的共享并下载您需要的任何ISO和软件 
```
smbclient -U username //10.0.0.2/Downloads -m SMB3 -W win get windows7x64.iso
```
完成此操作后，您可以返回到Ubuntu的VM设置，并将第一个NIC更改为NAT。

## 配置Guest VM
打开VirtualBox并创建基础虚拟机 - 我将分别创建名为Win10x64和Win7x64的Windows 7 32位和64位虚拟机。配置可以小一点。所以呢，这样子：


```
1个CPU

512MB RAM

10GB硬盘 

1个NIC连接到vboxnet0

```
在安装过程中，将所有VM的用户名设置为cuckoo。等待安装完成。

在每个VM中设置静态IP

```
Win10x64  -  192.168.56.10

Win7x64  -  192.168.56.15
```

然后：
```
禁用Windows防火墙

禁用UAC（从不通知）

禁用Windows更新
```

将最新的Python 2.7.x for Windows下载到您的Ubuntu服务器。将文件托管在方便的位置并启动简单的一个Web服务器
```
cd ~/Downloads cp ~/cuckoo/agents/agent.py ~/Downloads python -m SimpleHTTPServer
```
下载x64 MSI安装程序和Cuckoo代理 
```
192.168.51:8000/python-2.7.14.amd64.msi 192.168.51:8000/agent.py
```
在每个VM中手动安装Python。

打开一个Cuckoo代理。
```
Command Prompt as Administrator
```
在VM运行时，请按照以下步骤对其进行快照（对每个VM重复）：

```
VBoxManage snapshot "Win7x64" take "Win7x64_snap" --pause
VBoxManage controlvm "Win7x64" poweroff
VBoxManage snapshot "Win7x64" restorecurrent
```
在GUI中，它们应显示为 Saved

配置Cuckoo

```
vim ~/cuckoo/conf/virtualbox.conf
```

```
mode = headless - > 对测试很有用。mode = gui

machines = cuckoo1- > machines = Win1x64,Win7x64
加上你做过的。
```
cuckoo1是默认示例。每个VM都需要自己块。

```
[Win10x64]
label = Win10x64
platform = windows
ip = 192.168.56.10
snapshot = Win10x64_snap
​
[Win7x64]
label = Win7x64
platform = windows
ip = 192.168.56.15
snapshot = Win7x64_snap
```

run cuckoo:

```
. cuckoo/bin/activate cuckoo
```
web GUI 
```
cuckoo web runserver 192.168.45.128:8080
```
你可以提交样本了！