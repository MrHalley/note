## Vagrant


### 使用教程

[使用vagrant安装centos7](https://www.jianshu.com/p/9e0883d6132a)

[Vagrant自动创建Vmware虚拟机](https://blog.csdn.net/qq_33745102/article/details/119904146)

[vagrant官方文档](https://www.bookstack.cn/read/Vagrant/fa4d7ed9b438feed.md#%C2%BB%20Vagrant%20VMware%20Utility%20Service)

[使用XShell连接Vagrant](https://blog.csdn.net/qq_38826019/article/details/114848864)

```bash
# 初始化生成Vagrantfile文件
vagrant init
# 添加box
vagrant box add box_name(centos/7) box_addr(官网地址：https://app.vagrantup.com/boxes/search下载对应box)
# 查看box 列表
D:\虚拟电脑数据\VirtualBox VMs\centos7>vagrant box list
centos/7 (virtualbox, 0)
centos/7 (vmware_desktop, 2004.01)
# 按照指定的box启动
D:\虚拟电脑数据\VirtualBox VMs\centos7>vagrant up --provider virtualbox
# ssh连接
vagrant ssh
```



> linux配置密码连接（让xshell可连）

```bash
Vagrant ssh 进去系统之后
vi /etc/ssh/sshd_config
修改 PasswordAuthentication yes/no
重启服务 service sshd restar
```



> 配置固定ip

```bash
# 找到Vagrantfile中的对应配置项配置即可
config.vm.network "private_network", ip: "192.168.57.10"

# vmware中配置了固定Ip后，xshell连不上虚拟机解决步骤
# 可在Vmware->编辑->虚拟网络编辑器中看到子网ip为192.168.56.0的网卡Vmnet2，在Vmware->虚拟机->设置中找到网络适配器2，配置成自定义的Vmnet2,然后在计算机“网络和Internet设置”中找到Vmnet2的网络，修改适配器选项，TCP/IPV4 ->属性，使用下面的ip地址(ip地址：192.168.56.1  子网掩码：255.255.255.0)确定即可，可参考virtualbox对应的网卡配置

#vmware克隆后换IP地址
/etc/sysconfig/network-scripts/ifcfg-eth1
sudo systemctl restart network
```



> 配置阿里云yum源

```bash
# 下载对应系统版本的阿里云yum源
[root@hzk /]# wget -O /etc/yum.repos.d/CentOs-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

