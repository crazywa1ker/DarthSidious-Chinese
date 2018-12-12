# 使用ESXI和Vagrant搭建实验环境
## 环境设计
ESXi 6.5安装在物理机上，在隔离的虚拟网络上有多个VM。虚拟防火墙是内部网络的边界，并提供VPN访问。VPN访问将设置为直接连接到网络，但不提供域用户。
##域设计
暂无
## 服务计划

```
主机名             角色           OS
DC01              域控制器     Server 2012 R2

FS01              文件服务器     Server 2008 R2

WEB01             网络服务器   Server 2016 Tech Eval

WS01             工作站                 W10企业版

WS02            工作站                 W7企业版

CENT01          linux box           CentOS 7.4

FW01            防火墙              pfSense
```
## 准备
安装所有软件要求并下载必要的ISO。它们可以从MS评估中心或[The-Eye](https://the-eye.eu/public/MSDN/)获得。

## 硬件要求

- ESXi 6.5兼容的硬件（如果不兼容，可以使用6.0）

- 最低32 GB RAM

- ESXi驱动器 - 仅需8 GB

- 实际VM的驱动器 -  500 GB +

- 用于安装ESXi的USB驱动器 - 最小1 GB

- 需要一台独立的计算机来管理

## 软件要求
VMware

- ESXi的6.5版本

- VMware Workstation 1x.x.--版本，最新是14.0

- VMware的 ovftool

- vCenter

- vSphere客户端
## Orchestration
- [​Vagrant​]()

- Vagrant VMware ESXi 的插件- [josenk/vagrant-vmware-esxi: Vagrant 的一个插件增加了VMware的ESXi支持](https://github.com/josenk/vagrant-vmware-esxi)

- Vagrant 刷新重载 - [aidanns](https://github.com/aidanns)/[vagrant-reload​](https://github.com/aidanns/vagrant-reload)

- Vagrant WinRM 文件夹同步- [Cimpress-MCP](https://github.com/Cimpress-MCP)/[vagrant-winrm-syncedfolders​](https://github.com/Cimpress-MCP/vagrant-winrm-syncedfolders)

> 译者注：以上工具我也没用过，可自行通过gayhub了解怎么玩。
ISOs

## ISO

- Windows Server 2012 R2

- Windows Server 2016

- Windows 7企业版

- Windows 10企业版

- CentOS 7.4

## 安装ESXI
下载ESXI 6.5映像

- 使用[Rufus](http://rufus.akeo.ie/)从ESXI 镜像中制作一个可启动的USB key。

- 通过USB引导按照实验靶机，并按照说明在小型驱动器上安装ESXi。

-安装后，重新启动服务器。ESXi现在应该有可以从Web面板访问DHCP租用的IP地址。

- 此时设置静态IP避免ESXi网络适配器的IP在您执行操作时不断更改。

## 故障排除
故障排除与写入速度的SSD：https://communities.vmware.com/thread/554004

ESXi 6.5包含用于SATA AHCI控制器的新本机驱动程序（vmw_ahci），但这会导致许多控制器或者磁盘出现性能问题。

尝试禁用本机驱动程序并通过cmd恢复到较旧的sata-ahci驱动程序

```
esxcli system moduleset--enabled=false--module=vmw_ahci
```

## 启用ESXi shell和SSH
Vagrant ESXi插件需要SSH才能启用。

- 在ESXi主机的控制台上，按F2出现提示：提供凭据。

- 滚动到`故障排除选项`，然后按Enter键。

- 选择`启用ESXi shell`和`启用SSH`，然后在每个按Enter键一次

- 按Esc返回主控制台屏幕。

## 将数据存储添加到ESXi
- 添加大型驱动器，虚拟机将作为数据存储区存储在ESXi中。

- 在ESXi Web客户端中，按左侧窗格中的Storage

- 从菜单中选择后，按照说明操作---->New datastore

- 添加一个驱动器，给它命名，并将整个驱动器用作一个分区。

## 将网络配置添加到ESXi
在左侧窗格中选择`网络`

- 单击`添加标准交换机`，将其命名为`vSwitch1`



- 单击端口组，ADD端口组。

- 指定名称，将其分配给默认的虚拟交换机。Lab NetworkVLAN 0vSwitch0

## 安装Vagrant
安装Vagrant和插件


```
vagrant plugin install vagrant-vmware-esxi
vagrant plugin install vagrant-winrm-syncedfolders
vagrant plugin install vagrant-reload
​
vagrant plugin list
    vagrant-reload (0.0.1)
    vagrant-vmware-esxi (2.3.1)
    vagrant-winrm-syncedfolders (1.0.1)
```
（新方法） - 使用Packer构建VM
Packer帮助我们自动化将镜像准备好然后部署到虚拟机。
（旧方法） - 在Vagrant中下载操作系统

使用以下命令使用Vagrant下载所需的操作系统。出现提示时选择提供程序。建议从Vagrant的云服务器中选择没有内置任何配置管理的主机是。这些通常表示为。
```
vmware_desktop nocm
```

```
vagrant box add opentable/win-2008r2-enterprise-amd64-nocm
vagrant box add opentable/win-2012r2-standard-amd64-nocm
vagrant box add StefanScherer/windows_2016
vagrant box add opentable/win-7-enterprise-amd64-nocm
vagrant box add StefanScherer/windows_10`
```

https://app.vagrantup.com/opentable/boxes/win-2008r2-enterprise-amd64-nocm
https://app.vagrantup.com/opentable/boxes/win-2012-standard-amd64-nocm
https://app.vagrantup.com/StefanScherer/boxes/windows_2016
https://app.vagrantup.com/opentable/boxes/win-7-enterprise-amd64-nocm
https://app.vagrantup.com/StefanScherer/boxes/windows_10

## 老方法就是为每个系统准备镜像
虽然是麻烦了一点，但是可以减少配环境的劳动力。

- 建一个目录，命名为`PrepSever2016`。将VM的整个目录复制到新目录---`vagrant.d/boxes/repoNameOfVM`

- 在Workstation中引导VM之前，请设置文件共享，因为必须将文件传输到这个机器。

- 设置网络适配器，就可以在本地Web服务器或Github上托管文件，方便下载到该机器中。

- 继续在VM中启动机器并准备以下内容：

1.修复帐户

启用本地管理员帐户并删除Vagrant帐户

- 控制面板 - >用户帐户 - >管理另一个帐户 - >管理员 - >设置管理员帐户的密码 - >注销

- 使用新密码以管理员身份登录，进入控制面板 - >用户 - >删除用户帐户 - >删除流Vagant帐户 - >单击删除

2. 安装VMware Tools
通过VMware workstation界面完成。

3. Windows Update
使用WU.ps1脚本下载并安装操作系统的更新。

以管理员身份打开powershell.exe并运行 

```
Import-Module C:\Users\Administrator\Desktop\WU.ps1
```

在重新启动之前，必须多次执行此操作，直到无法再应用更新。只要继续运行它，直到没有了更多更新。

4.安装.Net框架

5.运行Sysprep
Sysprep将通过此处提供的XML文件完成：link (译者注：忘记加了链接)

- 将管理员和自动登录密码更改为正确的密码

- 更改时区。在这里查找微软时区值：https://msdn.microsoft.com/en-us/library/ms912391(v=winembedded.11).aspx

 - 使用以下命令执行sysprep。

 ```
C:\Windows\system32\sysprep\sysprep.exe /generalize /oobe /shutdown /unattend:c:\users\Administrator\Desktop\sysprep.xml
```
6.验证
现在应该关闭VM，我们想验证一切是否按预期工作。

- 转到VM  - >管理 - >克隆 - >完全克隆VM。（需要很长时间）

- 引导克隆的vm并验证是否已正确设置所有内容。

- 关闭并删除克隆或将其作为基础镜像。

- 制作已修复的VM的副本并将其放入文件夹中。

- 将文件夹重命名为Server2016或您喜欢的任何名称。

- 如果您的磁盘空间不足，则可以删除从Vagrant云服务或一开始下载的原始VM，请注意，如果出现问题，可能需要稍后使用它们。

- 拍快照，拍快照。


## 使用Vagrant部署VM
初始化Vagant

这个以及其他文件创建了非常重要的Vagrantfile，它保存了部署配置。

```
vagrant init
```

Vagrantfile配置

vmware esxi插件的文档包含示例和配置。

```
https://github.com/josenk/vagrant-vmware-esxi/wiki/Vagrantfile-examle:-Single-Machine,-fully-documented。
```
```
https://www.vagrantup.com/docs/vagrantfile/machine_settings.html 
```
给自己的实验靶机做一个标签，所以你可以有多个BOX，其实你的整个实验环境就是一个Vagrantfile。

将BOX的名称和指针设置为您在先前步骤中下载的BOX。winrm参数指定应使用WinRM（powershell远程控制盒）进行部署。可以为向域中添加靶机，设置某些系统参数等任务添加许多powershell脚本，自动化准备系统。

esxi参数位于底部。主机名必须指向管理网络虚拟交换机接口，当然必须设置密码。


```
Vagrant.configure("2") do |config|
config.vm.synced_folder ".", "/vagrant", disabled: true
​
  config.vm.define "WEB01" do |config|
    config.vm.box = "Server2016"
    config.vm.hostname="WEB01"
    config.vm.guest = :windows
    config.vm.communicator = "winrm"
    config.vm.synced_folder "C:\\Users\\chris\\Google Drive\\Hacking\\beelabs\\AD_Files", "C:\\windows\\temp", type: "winrm"
    config.vm.boot_timeout = 100
    config.vm.graceful_halt_timeout = 100
    config.winrm.timeout = 120
    config.winrm.username = "Administrator"
    config.winrm.password = "PASSWORD"
    config.winrm.transport = :plaintext
    config.winrm.basic_auth_only = true
    config.vm.provision "shell", inline: "Rename-Computer -NewName WEB01"
    config.vm.provision :reload
​
    config.vm.provider :vmware_esxi do |esxi|
      esxi.esxi_hostname = "10.0.0.10"
      esxi.esxi_username = "root"
      esxi.esxi_password = "PASSWORD"
      esxi_virtual_network = "Lab Network"
      esxi.esxi_disk_store = "VMs"
      esxi.guest_memsize = "2048"
      esxi.guest_numvcpus = "2"
      esxi.mac_address = ["00:50:56:3f:01:01"]
    end
  end
  end
end
```
然后部署机器运行

```
vagrant status
```
并修复最终的错误
然后部署机器运行

```
vagrant up
```
将使用Vagrantfile，应用它，并使用OVFtool使用上述插件将其部署到ESXi主机。

如果这个机器在部署之后关闭，需要在不配置它的情况下启动它的话，请执行以下内容
```
vagrant up BOX01 --no-provision
```
在部署和配置该机器之后，关闭它并拍摄快照。这也可以通过使用快照和回滚Vagrant来完成。

```
vagrant snapshot push //拍快照
```

```
vagrant snapshot pop  //回滚
```


```
vagrant snapshot list //列出快照
```

END

