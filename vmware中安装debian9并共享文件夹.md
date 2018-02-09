安装过程不做表述，主要是debian中vmware-tools的的安装，比起centos有一些坑

主要是open-vm-tools 代替了官方 VMware Tools，如果你执意要安装官方版本，它会提醒你：

open-vm-tools are available from the OS vendor and VMware recommends using open-vm-tools.See http://kb.vmware.com/kb/2073803 for more information.Do you still want to proceed with this legacy installer?

既然大部分发行版的官方仓库里面都有 open-vm-tools ，直接安装即可。

Ubuntu / Debian:

sudo apt install open-vm-tools

CentOS / Fedora:

sudo dnf install open-vm-tools

Arch Linux

sudo pacman -S open-vm-tools

有几个注意的点：

如果要实现文件夹共享，需要安装 open-vm-tools-dkms

桌面环境还需要安装 open-vm-tools-desktop 以支持双向拖放文件

Arch Linux 用户如果需要双向拖放文件，还需要安装 gtkmm 和 gtkmm3

挂载文件夹

最后在命令行中输入如下命令：

mount -t vmhgfs .host:/ /mnt/hgfs

如果没有权限则需要切换到root用户

如果报错 ERROR: can not mount filesystem: No such device，则输入如下命令：

sudo vmhgfs-fuse .host:/ /mnt/hgfs

注意：挂载的文件夹必须是空文件夹，否则这里会报错。

另外就是debian不同于centos，需要每次开机手动执行一次挂载命令

Debian 9 默认不带 /etc/rc.local 文件，而 rc.local 服务却还是自带的

首先查看状态，默认情况下这个服务是关闭的状态

systemctl status rc-local

为了解决这个问题，我们需要手工添加一个 /etc/rc.local 文件

把需要开机启动的命令添加到 /etc/rc.local 文件，丢在 exit 0 前面

#!/bin/bash

# rc.local

# This script is executed at the end of each multiuser runlevel.

# Make sure that the script will "exit 0" on success or any other

# value on error.

#

# In order to enable or disable this script just change the execution

# bits.

#

# By default this script does nothing.

sudo vmhgfs-fuse .host:/ /mnt/hgfs

exit 0

EOF
然后赋予权限

chmod +x /etc/rc.local

接着启动 rc-local 服务

systemctl start rc-local

再次查看状态

systemctl status rc-local

重启即可
