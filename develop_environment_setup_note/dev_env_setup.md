### nodejs 安装

https://github.com/nodejs/help/wiki/Installation

```sh
cd ~ && wget https://nodejs.org/dist/v14.16.0/node-v14.16.0-linux-x64.tar.xz
mkdir -p /usr/local/lib/nodejs

cd /data/home/spider/ && tar -xJvf node-v14.16.0-linux-x64.tar.xz -C /usr/local/lib/nodejs

vim ~/.bash_profile
export PATH=/usr/local/lib/nodejs/node-v14.16.0-linux-x64/bin:$PATH
source ~/.bash_profile

```

# python 开发环境配置

### 安装必备包

- centos minimal server 安装后缺少必备的软件包

```sh
yum -y update

# ping, ifconfig 等工具
yum -y install net-tools

yum -y install vim wget

yum -y install gcc

# centos 8
yum groupinstall "Development Tools"

# yum-config-manager: command not found
yum -y install yum-utils
```


### centos ssh 远程连接

- 参考:
  - https://www.jianshu.com/p/41036a2fdb28

- 安装 ssh-server, 修改配置文件

```sh
# 要确保 CentOS7 安装了 openssh-server
yum list installed | grep openssh-server
# openssh-server.x86_64                  6.6.1p1-22.el7                  @anaconda

# 如果没有安装, 就使用如下命令安装
yum install openssh-server

# 修改 ssh 配置文件
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

vim /etc/ssh/sshd_config
```

- /etc/ssh/sshd_config 文件修改内容

```sh

# 将文件中, 关于监听端口、监听地址前的 # 号去除
Port 22
ListenAddress 0.0.0.0

# 允许 root 登录
PermitRootLogin yes

# 允许使用 ssh key 验证登录
PubkeyAuthentication yes

# 验证文件所在路径
# The default is to check both .ssh/authorized_keys and .ssh/authorized_keys2
# but this is overridden so installations will only check .ssh/authorized_keys
AuthorizedKeysFile      .ssh/authorized_keys

# 允许使用 密码 验证登录
PasswordAuthentication yes
```

- 重启服务并验证

```sh
# 重启 sshd 服务
# ubuntu
service sshd restart

# centos 7+
systemctl restart sshd

# 检查 sshd 服务是否已经开启
ps -e | grep sshd

# 检查 22 号端口是否开启监听
netstat -an | grep 22
```

#### 阿里云ssh连接失败
- 参考
  - https://help.aliyun.com/knowledge_detail/41489.html?spm=a2c4g.11186631.2.1.REIRbS

- 问题现象
  - 当您通过SSH客户端登录ECS Linux实例时，输入正确的账号密码，也会出现类似如下错误信息。

```
Symptom

Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
sshd[10826]: Connection closed by XX.XX.XX.XX.
Disconnected: No supported authentication methods available.
```
- 原因分析
  - 参数 PasswordAuthentication 的默认值为 yes，SSH服务将其值置为 no 以禁用密码验证登录，导致此类故障。需要修改 PasswordAuthentication 配置解决此问题。

- 解决方法
  - 建议在修改配置文件之前 创建快照 备份数据。以CentOS 6.8为例：

通过 管理终端 连接并登录到Linux实例。

执行命令 cat /etc/ssh/sshd_config 查看SSH服务配置，并注意是否包含类似如下配置：

Config

vi /etc/ssh/sshd_config
将参数 PasswordAuthentication 设置为 yes，或者注释掉 PasswordAuthentication 这一行

```bash
# 重启SSH服务。
service ssh restart

# 如果使用 CentOS7 以上的镜像，执行以下命令重启SSH服务。
systemctl restart sshd
```


### linux ssh 免密登录

- Windows 生成 ssh key

  - 安装 git for windows
  - 打开 git bash, 使用如下命令生成 ssh key

```sh
ssh-keygen -t rsa
# 生成两个文件 ~/.ssh/id_rsa, ~/.ssh/id_rsa.pub
# windows 保存在 C:\User\David\.ssh 目录下

```

- Linux 配置免密码登录
  - 使用用户名和密码登录到远程主机中, 编辑 *~/.ssh/authorized_keys* 文件, 把生成的 id_rsa.pub 中的内容粘贴进去
  - 想要对哪个用户免密码登录, 就修改哪个用户对应的 authorized_keys 文件

```sh
# 想对某个用户免密登录, 就使用此用户执行 创建 .ssh vim chmod 的 4 个操作
# 如果 .ssh 目录不存在, 就创建
mkdir ~/.ssh

# 编辑 ~/.ssh/authorized_keys 文件, 把 windows 中生成的 id_rsa.pub 粘贴到 authorized_keys 文件中
vim ~/.ssh/authorized_keys

# 修改 .ssh 目录的权限
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys

drwx------   2 root root  4.0K Mar 11 10:59 .ssh

# 修改 authorized_keys 的权限
chmod 600 ~/.ssh/authorized_keys

-rw------- 1 root root 1365 Mar 11 10:59 authorized_keys

# 重启 sshd 服务
# ubuntu
service sshd restart
# centos
systemctl restart sshd
```

#### ssh server refused our key 的错误

- 参考:
  - https://webcrafter.blogspot.com/2014/12/sshserver-refused-our-key.html
  - https://aws.amazon.com/cn/premiumsupport/knowledge-center/ec2-server-refused-our-key/

使用 stat 命令确保主目录下的文件权限正确。以下是正确权限的列表：

Linux 主目录（例如 /home）应为 (0755/drwxr-xr-x)
用户的主目录（例如 /home/david/）应为 (0700/drwx------)。
.ssh 目录权限（例如 /home/david/.ssh）应为 (0700/drwx------)。
authorized_keys 文件权限（例如 /home/david/.ssh/authorized_keys）应为 (0600/-rw-------)。 

```sh
# 修改 .ssh 目录的权限
chmod 700 ~/.ssh
# 修改 authorized_keys 的权限
chmod 600 ~/.ssh/authorized_keys
```

### centos 使用国内源

- 安装 ius 源 (centos7)
  - https://ius.io/

```sh
yum install \
https://repo.ius.io/ius-release-el7.rpm \
https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

yum clean all
yum makecache
```

- centos 使用 163 源 (centos 7)
  - http://mirrors.163.com/.help/centos.html

```sh
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

wget -O /etc/yum.repos.d/epel.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo

yum clean all
yum makecache
```


- 安装 EPEL 源 (centos8)

  - https://fedoraproject.org/wiki/EPEL

```sh
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

# on CentOS 8 it is recommended to also enable the PowerTools repository since EPEL packages may depend on packages from it:
dnf config-manager --set-enabled PowerTools
```

- EPEL 使用 aliyun 源 (centos7)
  - https://developer.aliyun.com/mirror/
  - https://developer.aliyun.com/mirror/epel
  - https://developer.aliyun.com/group/mirror#/


```sh
mv /etc/yum.repos.d/epel.repo /etc/yum.repos.d/epel.repo.backup
mv /etc/yum.repos.d/epel-testing.repo /etc/yum.repos.d/epel-testing.repo.backup

wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo

yum clean all
yum makecache
```

-  EPEL 使用 aliyun 源 (centos 8)

https://developer.aliyun.com/mirror/epel

```sh
# 1. 备份(如有配置其他epel源)

mv /etc/yum.repos.d/epel.repo /etc/yum.repos.d/epel.repo.backup
mv /etc/yum.repos.d/epel-testing.repo /etc/yum.repos.d/epel-testing.repo.backup

# 2. 下载新repo 到/etc/yum.repos.d/

# 1）安装 epel 配置包
yum install -y https://mirrors.aliyun.com/epel/epel-release-latest-8.noarch.rpm

# 2）将 repo 配置中的地址替换为阿里云镜像站地址
sed -i 's|^#baseurl=https://download.fedoraproject.org/pub|baseurl=https://mirrors.aliyun.com|' /etc/yum.repos.d/epel*
sed -i 's|^metalink|#metalink|' /etc/yum.repos.d/epel*

```

- centos 8 使用 aliyun 源

https://developer.aliyun.com/mirror/centos

```sh
# 备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 下载
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-8.repo

# 非阿里云ECS用户会出现 Couldn't resolve host 'mirrors.cloud.aliyuncs.com' 信息，不影响使用。用户也可自行修改相关配置: eg:
sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS
```

- 可能出现的 bug
  - 添加 centos 阿里云源之后, 出现以下警告:
  - 这是因为 aliyun 的 CentOS-Base.repo 中已经包含了 AppStream extras PowerTools centosplus 这几个源, 把原来自带的源删除或重命名即可

```sh
[root@centos yum.repos.d]# yum clean all
Repository AppStream is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository PowerTools is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration

mv CentOS-AppStream.repo CentOS-AppStream.repo.backup
mv CentOS-Extras.repo CentOS-Extras.repo.backup
mv CentOS-PowerTools.repo CentOS-PowerTools.repo.backup
mv CentOS-centosplus.repo CentOS-centosplus.repo.backup

```

#### delete repos

- reference
  - https://www.if-not-true-then-false.com/2010/yum-remove-repo-repository-yum-disable-repo-repository/

```sh
# remove rpmfusion repo
# Search Repository RPM-package with following command:
rpm -qa |grep -i rpmfusion
# rpmfusion-free-release-7-4.noarch
yum remove rpmfusion-free-release-7-4.noarch

# remove nux repo
rpm -aq | grep -i nux
# selinux-policy-targeted-3.13.1-266.el7_8.1.noarch
# python-linux-procfs-0.4.11-4.el7.noarch
# libselinux-2.5-15.el7.x86_64
# selinux-policy-3.13.1-266.el7_8.1.noarch
# linux-firmware-20191203-76.gite8a0f4c.el7.noarch
# libselinux-python-2.5-15.el7.x86_64
# util-linux-2.23.2-63.el7.x86_64
# libselinux-devel-2.5-15.el7.x86_64
# nux-dextop-release-0-5.el7.nux.noarch
# libselinux-utils-2.5-15.el7.x86_64

yum remove nux-dextop-release-0-5.el7.nux.noarch

```

### 虚拟机安装 ubuntu 安装 open-vm-tools

- 参考
  - https://www.jianshu.com/p/687acbfd21a5

安装 VMware Tools 经常会出现兼容性不好, 系统之间复制文件失灵, 并且安装时提示建议使用 open-vm-tools, 于是放弃 vmware-tools 的安装, 尝试使用open-vm-tools.

open-vm-tools 是 VMware Tools 的开源实施, 由一套虚拟化实用程序组成, 这些程序可增强虚拟机在 VMware 环境中的功能, 使管理更加有效. open-vm-tools 的主要目的是使操作系统供应商及/或社区以及虚拟设备供应商将 VMware Tools 绑定到其产品发布中. 

```bash
apt search open-vm

# Sorting... Done
# Full Text Search... Done
# open-vm-tools/xenial-updates 2:10.2.0-3~ubuntu0.16.04.1 amd64
#   Open VMware Tools for virtual machines hosted on VMware (CLI)
# 
# open-vm-tools-desktop/xenial-updates 2:10.2.0-3~ubuntu0.16.04.1 amd64
#   Open VMware Tools for virtual machines hosted on VMware (GUI)
# 
# open-vm-tools-dev/xenial-updates 2:10.2.0-3~ubuntu0.16.04.1 amd64
#   Open VMware Tools for virtual machines hosted on VMware (development)
# 
# open-vm-tools-dkms/xenial-updates,xenial-updates 2:10.0.7-3227872-5ubuntu1~16.04.2 all
#   Open VMware Tools vmxnet kernel module (deprecated)

# open-vm-tools 支持共享文件夹
# 桌面环境安装 open-vm-tools-desktop 以支持双向拖放文件
apt install open-vm-tools open-vm-tools-dev open-vm-tools-desktop

```


### centos 挂载新硬盘

- 参考:
  - https://www.jianshu.com/p/ef670899bf1f
  - https://droidyue.com/blog/2020/04/12/mount-a-new-disk-on-centos-linux/


```sh
# 1. 查看当前硬盘与目录挂载情况, 假设未挂载的硬盘是 /dev/sdb
df -h
# Filesystem           Size  Used Avail Use% Mounted on
# devtmpfs             7.7G     0  7.7G   0% /dev
# tmpfs                7.8G     0  7.8G   0% /dev/shm
# tmpfs                7.8G   18M  7.7G   1% /run
# tmpfs                7.8G     0  7.8G   0% /sys/fs/cgroup
# /dev/mapper/cl-root   50G  2.1G   48G   5% /
# /dev/sdb2            976M  162M  748M  18% /boot
# /dev/sdb1            599M  6.8M  593M   2% /boot/efi
# /dev/mapper/cl-home   53G  406M   52G   1% /home
# tmpfs                1.6G     0  1.6G   0% /run/user/1000
# tmpfs                1.6G     0  1.6G   0% /run/user/0


# 2. 數據如何磁盘挂载情况
lsblk

# NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
# sda           8:0    0 931.5G  0 disk
# └─sda1        8:1    0 931.5G  0 part
# sdb           8:16   0 111.8G  0 disk
# ├─sdb1        8:17   0   600M  0 part /boot/efi
# ├─sdb2        8:18   0     1G  0 part /boot
# └─sdb3        8:19   0 110.2G  0 part
#   ├─cl-root 253:0    0    50G  0 lvm  /
#   ├─cl-swap 253:1    0   7.9G  0 lvm  [SWAP]
#   └─cl-home 253:2    0  52.4G  0 lvm  /home
# sdc           8:32   1  14.9G  0 disk
# └─sdc1        8:33   1  14.9G  0 part
# sr0          11:0    1  1024M  0 rom


# 3. 查看新添加的硬盘
fdisk -l

# 首次使用需要初始化硬盘, 假设 sdb 是新添加的硬盘
fdisk /dev/sdb

# 之后依次 输入 n , p ,1 ,w, 固定使用方式
# n 表示 new, 新添加磁盘, p 表示 partition 分区, 1 表示只添加一个分区, w 表示 write, 把修改写入到文件
# 如果已经存在分区, 根据实际情况可能需要 使用 d 命令删除掉原来的所有分区

[root@centos ~]# fdisk /dev/sda

Welcome to fdisk (util-linux 2.32.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table


Command (m for help): d
Selected partition 1
Partition 1 has been deleted.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-1953525167, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-1953525167, default 1953525167):

Created a new partition 1 of type 'Linux' and of size 931.5 GiB.
Partition #1 contains a ntfs signature.

Do you want to remove the signature? [Y]es/[N]o: y

The signature will be removed by a write command.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.


# 创建文件系统, 格式化硬盘
mkfs.ext4 /dev/sdb1
# mkfs -t ext4 /dev/sdb1

# 创建挂载点
mkdir /data

# 手动挂载硬盘
mount /dev/sdb1 /data 

# 验证硬盘已经成功挂载
lsblk

# 开机自动挂载硬盘

# 备份文件
cp /etc/fstab /etc/fstab.backup

# 修改 /etc/fstab, 在后面添加一行
# /dev/sdb1               /data                   ext4    defaults        0 0
vim /etc/fstab


# 验证 fstab 配置是否正确
mount -a

# 重启服务器验证开机自动挂载硬盘
```

- fstab 文件内容

```
# /etc/fstab
# Created by anaconda on Thu Jul 23 23:05:21 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=c90f29be-5caa-4483-b979-d113223a8829 /boot                   xfs     defaults        0 0
UUID=FF12-33AB          /boot/efi               vfat    umask=0077,shortname=winnt 0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0

/dev/sdb1               /data                   ext4    defaults        0 0

```

### 创建 spider 用户

- 参考
  - https://blog.csdn.net/CAU_Ayao/article/details/83502880

```sh

# 查看你正在使用的shell解释器，一般是bash。

echo $$ 可以查看shell的PID

echo $0 简单查看正在使用的shell解释器

echo $SHELL 查看解释器的同时显示其所在目录

/bin 目录下存放的二进制可执行文件

# 链接：https://www.jianshu.com/p/ab056111ee98

# 创建 spider 用户
# useradd命令参数意义:
# -r: 建立系统账号
# -m: 自动建立用户的登入目录
# -s: 指定用户登入后所使用的shell
sudo useradd -m spider -d /home/spider
# /home 下硬盘有限，故建在 /data 目录下
sudo useradd -m spider -d /data/home/spider
sudo useradd -m spider -d /home/spider -s /bin/bash

# 当创建 spider 时指定了 /data/home/spider，但是无 /data/home 目录，创建目录就会失败，会先创建用户
# 然后手动创建 /data/home/spider
# 此时 /data/home/spider 下就没有 bashrc 等文件了，可以从 /home/root 下面复制来其它文件

# 修改 spider 用户密码
sudo passwd spider

# 修改 /data 目录的所有者, 把所有者和用户组都修改为 spider
chown spider:spider /data
```

### 虚拟环境配置与使用

#### windows 使用 virutalenv 创建虚拟环境


```sh
# windows 升级pip到最新版
python -m install --upgrade pip setuptools wheel


pip install ipython

# 3. 安装虚拟环境

pip install virtualenv
# pip install virtualenvwrapper
pip install virtualenvwrapper-win

# 设置 WORKON_HOME 环境变量
变量名: WORKON_HOME
变量值: C:\Develop\Envs

```

### Path 环境变量

JAVA_HOME
C:\Develop\Java\jdk12

添加 Path 变量

%JAVA_HOME%\bin

编译 javac 文件名.java
例如 javac HelloWorld.java

执行: java 类名
例如: java HelloWorld



##### Windows 修改 pip 源

创建如下文件 %HOMEPATH%\pip\pip.ini

```ini
[global]
index-url = https://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com
```

#### linux 安装 pyenv


##### ubuntu 安装 git

- https://git-scm.com/download/linux
- For Ubuntu, this PPA provides the latest stable upstream Git version

```bash
apt update
apt upgrade
apt -y install software-properties-common
add-apt-repository ppa:git-core/ppa
apt update
apt upgrade
apt install git

```

##### centos 7 安装 git

- 源码安装 git

- 参考:
  - https://github.com/git/git/blob/master/INSTALL
  - https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  - https://github.com/git/git/releases

```sh

# 安装 依赖
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker

sudo yum groupinstall "Development Tools"
sudo yum install gettext-devel openssl-devel perl-CPAN perl-devel zlib-devel

```

##### centos 8 安装 git ??

- 首先需要启用 epel 源

```sh
# 安装 git 
dnf install git-all

# 查看 git 的版本
git --version
# git version 2.18.4


```

##### 使用 pyenv-installer 安装 pyenv

  - https://github.com/pyenv/pyenv-installer
  - https://realpython.com/intro-to-pyenv/
  - https://amaral.northwestern.edu/resources/guides/pyenv-tutorial

- root 用户或 root 权限用户安装支持包
  - https://github.com/pyenv/pyenv/wiki/Common-build-problems

Ubuntu
```bash
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev python-openssl git
```

Centos
```bash

yum install @development zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel xz xz-devel libffi-devel findutils

yum -y install zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel \
openssl-devel xz xz-devel libffi-devel findutils
```

- spider 用户 使用 pyenv-installer 安装 pyenv 全家桶

```bash
curl https://pyenv.run | bash

# or
# curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash

```

- This will install pyenv along with a few plugins that are useful:

  - pyenv: The actual pyenv application
  - pyenv-virtualenv: Plugin for pyenv and virtual environments
  - pyenv-update: Plugin for updating pyenv
  - pyenv-doctor: Plugin to verify that pyenv and build dependencies are installed
  - pyenv-which-ext: Plugin to automatically lookup system commands

```
WARNING: seems you still have not added 'pyenv' to the load path.

# Load pyenv automatically by adding
# the following to ~/.bashrc:

export PATH="/home/spider/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

- 阿里云 pyenv 无法安装

因为阿里云无法连接到 github, 导致 pyenv 无法安装

查看 pyenv-installer 的源码，看都使用了哪些 地址，结果发现只使用了一个地址，即  https://github.com

在其它电脑上 ping github.com, 如 在 ucloud 上 ping, 得到 10.9.254.131

修改 /etc/hosts, 添加一行 '10.9.254.131    github.com'

刷新 dns `service nscd restart` 或 `nscd -i hosts`

就可以安装 pyenv 了

- 参考
  - https://zhuanlan.zhihu.com/p/314071453
  - https://yq.aliyun.com/articles/713169
  - https://developer.aliyun.com/article/623838
  - https://blog.naibabiji.com/skill/github.html


- 把 pyenv 添加到环境变量中

```bash
vim ~/.bashrc
# add the following 

export PATH="/home/spider/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"

source ~/.bashrc
```

##### 查看并安装指定 python 版本

```bash
# 查找 python3.6, 3.7, 3.8 版本
pyenv install --list | grep " 3\.[678]"

# 使用 taobao python 镜像

wget https://npm.taobao.org/mirrors/python/3.8.8/Python-3.8.8.tar.xz -P ~/.pyenv/cache
wget https://npm.taobao.org/mirrors/python/3.8.9/Python-3.8.9.tar.xz -P ~/.pyenv/cache
wget https://npm.taobao.org/mirrors/python/3.8.10/Python-3.8.10.tar.xz -P ~/.pyenv/cache
wget https://npm.taobao.org/mirrors/python/3.6.13/Python-3.6.13.tar.xz -P ~/.pyenv/cache

# 手动下载此文件，上传到 ~/.pyenv/cache 目录中
https://www.python.org/ftp/python/3.6.13/Python-3.6.13.tar.xz

mkdir -p ~/.pyenv/cache
cd ~/.pyenv/cache

# 执行以下命令安装 Python
pyenv install 3
pyenv virtualenv 3.6.13 report
cd /data/ && pyenv local common

cd /data/spider/scrapy_projects_report_cost && pyenv local report

yum install gcc

# 安装 python 3.7.2
pyenv install -v 3.7.2
# 安装 python 3.6.9
pyenv install 3.6.8
pyenv install 3.6.9

# 根据已有 python 版本创建虚拟环境
pyenv virtualenv 3.6.9 goods_crawler
pyenv virtualenv 3.6.9 ttbs_crawler

```

##### 创建目录, 并设置 python 版本

```bash

# 查看所有的本地版本
pyenv versions

mkdir -p /data/spider/goods_crawler
cd /data/spider/goods_crawler

# 设置 python 环境

pyenv local goods_crawler

```

##### 配置 pip 国内加速器

```bash
mkdir ~/.pip && vim ~/.pip/pip.conf
vim ~/.pip/pip.conf
```

```conf
# pip.conf 文件内容
[global]
index-url = https://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com
```

### 安装 pyenv 失败

```sh
[spider@crawler001 ~]$ curl https://pyenv.run | bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   285  100   285    0     0    112      0  0:00:02  0:00:02 --:--:--   112
Cloning into '/data/home/spider/.pyenv'...
fatal: unable to access 'https://github.com/pyenv/pyenv.git/': Unable to communicate securely with peer: requested domain name does not match the server's certificate.
Failed to git clone https://github.com/pyenv/pyenv.git
```

- 原因
    - 修改了 /etc/hosts 中的 github 的地址为  8.10.xxx.xxx
    - 再次修改为其它可用的地址即可

### cuda 安装及配置

#### windows 安装 cuda

- 参考:
  - https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html

- 安装 Visual Studio
  - 下载 Visual Studio 2019 Community
  - https://visualstudio.microsoft.com/zh-hans/
  - 安装, 不选择其它组件即可
  - 安装位置: C:\Develop\VisualStudio

- 下载并安装 CUDA 10.2
  - https://developer.nvidia.com/cuda-toolkit-archive 
  - 安装位置
    - 主程序: C:\Develop\CUDA\Packages
    - 文档: C:\Develop\CUDA\Documentation
    - 示例: C:\Develop\CUDA\Samples



#### Centos 安装 cuda

- 参考:
  - https://developer.nvidia.com/cuda-downloads
  - https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html
  - https://blog.csdn.net/llh_1178/article/details/91045056
  - https://blog.csdn.net/lzcong1986/article/details/81050639
  - https://www.cnblogs.com/ttrrpp/p/12175608.html


```sh

# 判断 GPU 是否支持
1. Verify You Have a CUDA-Capable GPU

# 查看 lspci 命令是由哪个包提供的
yum whatprovides */lspci

# Loaded plugins: fastestmirror, nvidia
# Loading mirror speeds from cached hostfile
#  * base: mirrors.nju.edu.cn
#  * elrepo: mirrors.neusoft.edu.cn
#  * extras: mirrors.nju.edu.cn
#  * updates: mirrors.163.com
# ius/x86_64/filelists                                                                        | 614 kB  00:00:00
# updates/7/x86_64/filelists_db                                                               | 1.7 MB  00:00:00
# pciutils-3.5.1-3.el7.x86_64 : PCI bus related utilities
# Repo        : base
# Matched from:
# Filename    : /usr/sbin/lspci

# 安装 pciutils
yum install pciutils

# 查看本机是否有支持 cuda 的 nvidia GPU
lspci | grep -i nvidia
# 01:00.0 VGA compatible controller: NVIDIA Corporation GK208B [GeForce GT 730] (rev a1)
# 01:00.1 Audio device: NVIDIA Corporation GK208 HDMI/DP Audio Controller (rev a1)

# 如果没有出现支持的硬件, 可能需要更新 pci 硬件列表
update-pciids
#   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
#                                  Dload  Upload   Total   Spent    Left  Speed
# 100  260k  100  260k    0     0   6524      0  0:00:40  0:00:40 --:--:--  9474
# Done.

# tensorflow

# 如果 gpu 在本网页的 gpu 列表中, 就表示支持 cuda
# https://developer.nvidia.com/cuda-gpus

# 查看 linux 是支持 cuda 的系统
2. Verify You Have a Supported Version of Linux

# 查看 linux 发行版
uname -m && cat /etc/*release

# [root@centos-7 ~]# uname -m && cat /etc/*release
# x86_64
# CentOS Linux release 7.8.2003 (Core)
# NAME="CentOS Linux"
# VERSION="7 (Core)"
# ID="centos"
# ID_LIKE="rhel fedora"
# VERSION_ID="7"
# PRETTY_NAME="CentOS Linux 7 (Core)"
# ANSI_COLOR="0;31"
# CPE_NAME="cpe:/o:centos:centos:7"
# HOME_URL="https://www.centos.org/"
# BUG_REPORT_URL="https://bugs.centos.org/"

# CENTOS_MANTISBT_PROJECT="CentOS-7"
# CENTOS_MANTISBT_PROJECT_VERSION="7"
# REDHAT_SUPPORT_PRODUCT="centos"
# REDHAT_SUPPORT_PRODUCT_VERSION="7"

# CentOS Linux release 7.8.2003 (Core)
# CentOS Linux release 7.8.2003 (Core)

# 对比 cuda relase note 中的结果
https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html
https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#system-requirements


3. Verify the System Has gcc Installed

# 查看安装的 gcc 版本
gcc --version

# [root@centos-7 ~]# gcc --version
# gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
# Copyright (C) 2015 Free Software Foundation, Inc.
# This is free software; see the source for copying conditions.  There is NO
# warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.


# 查看 linux 内核和 安装的开发包
4. Verify the System has the Correct Kernel Headers and Development Packages Installed

# https://phoenixnap.com/kb/how-to-check-centos-version
# 查看内核版本
uname -r
# [root@centos-7 ~]# uname -r
# 3.10.0-1127.13.1.el7.x86_64

hostnamectl
# [root@centos-7 ~]# hostnamectl
#    Static hostname: centos-7
#          Icon name: computer-desktop
#            Chassis: desktop
#         Machine ID: 02291c7cb5a64259aeacf4e59c3b3bad
#            Boot ID: 9b5511f9edbb4bc183f81a44e978bdd7
#   Operating System: CentOS Linux 7 (Core)
#        CPE OS Name: cpe:/o:centos:centos:7
#             Kernel: Linux 3.10.0-1127.13.1.el7.x86_64
#       Architecture: x86-64

5. 安装 kernel development package

# 注意: 如果更新了 kernel 的版本, 必须要重新运行此命令安装与之对应的 开发包
# centos 7
yum install kernel-devel-$(uname -r) kernel-headers-$(uname -r)

# CentOS 8
dnf install kernel-devel-$(uname -r) kernel-headers-$(uname -r)


6. 选择安装方法 Choose an Installation Method

# https://developer.nvidia.com/cuda-downloads
# https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=CentOS&target_version=8&target_type=rpmnetwork

# 这里使用 rpm network 的方式安装
# 安装 cuda 官方源
sudo dnf config-manager --add-repo http://developer.download.nvidia.com/compute/cuda/repos/rhel8/x86_64/cuda-rhel8.repo


7. 使用 aliyun cuda 源

# 使用 nvidia 的 cuda 源时总是下载出错, 需要使用国内源

# 参考:
# https://developer.aliyun.com/mirror/nvidia-cuda
# https://mirrors.aliyun.com/nvidia-cuda/

# 查看 cuda 源的内容
cat /etc/yum.repos.d/cuda-rhel8.repo
[cuda-rhel8-x86_64]
name=cuda-rhel8-x86_64
baseurl=https://developer.download.nvidia.com/compute/cuda/repos/rhel8/x86_64
enabled=1
gpgcheck=1
gpgkey=https://developer.download.nvidia.com/compute/cuda/repos/rhel8/x86_64/7fa2af80.pub
module_hotfixes=1


# 替换其中的
# https://developer.download.nvidia.com/compute/cuda/repos/rhel8/x86_64
# 为
# https://mirrors.aliyun.com/nvidia-cuda/rhel8/x86_64/
# 得到

[cuda-rhel8-x86_64]
name=cuda-rhel8-x86_64
baseurl=https://mirrors.aliyun.com/nvidia-cuda/rhel8/x86_64/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/nvidia-cuda/rhel8/x86_64/7fa2af80.pub
module_hotfixes=1

8. 安装 cuda
# https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=CentOS&target_version=8&target_type=rpmnetwork
# https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-installation-actions

dnf clean all
dnf clean expire-cache
dnf -y module install nvidia-driver:latest-dkms
dnf -y install cuda-10-2


# 后续安装步骤 Post-installation Actions
9. 环境变量设置 Environment Setup
# The PATH variable needs to include $ export PATH=/usr/local/cuda-11.0/bin${PATH:+:${PATH}}. Nsight Compute has moved to /opt/nvidia/nsight-compute/ only in rpm/deb installation method. When using .run installer it is still located under /usr/local/cuda-11.0/.

export PATH=/usr/local/cuda-10.2/bin${PATH:+:${PATH}}

# In addition, when using the runfile installation method, the LD_LIBRARY_PATH variable needs to contain /usr/local/cuda-11.0/lib64 on a 64-bit system, or /usr/local/cuda-11.0/lib on a 32-bit system

# To change the environment variables for 64-bit operating systems:

export LD_LIBRARY_PATH=/usr/local/cuda-10.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

# LD_LIBRARY_PATH详解
# https://www.jianshu.com/p/a62e1d327023

# 把环境变量添加到 bashrc 中
# 编辑并添加如下两行
vim ~/.bashrc

export PATH=/usr/local/cuda-10.2/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-10.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}


# 推荐配置
# 6.2. Recommended Actions

10. 安装持久化服务 Install Persistence Daemon
# NVIDIA is providing a user-space daemon on Linux to support persistence of driver state across CUDA job runs. The daemon approach provides a more elegant and robust solution to this problem than persistence mode. For more details on the NVIDIA Persistence Daemon, see the documentation here.

# The NVIDIA Persistence Daemon can be started as the root user by running:
/usr/bin/nvidia-persistenced --verbose


# 查看安装的 cuda 版本
# - 参考: https://www.cnblogs.com/ttrrpp/p/12175608.html

# 查看 nvidia driver 的版本
cat /proc/driver/nvidia/version
# NVRM version: NVIDIA UNIX x86_64 Kernel Module  450.51.06  Sun Jul 19 20:02:54 UTC 2020
# GCC version:  gcc version 8.3.1 20191121 (Red Hat 8.3.1-5) (GCC)

nvcc --version
# nvcc: NVIDIA (R) Cuda compiler driver
# Copyright (c) 2005-2019 NVIDIA Corporation
# Built on Wed_Oct_23_19:24:38_PDT_2019
# Cuda compilation tools, release 10.2, V10.2.89

# 查看正在运行的任务
nvidia-smi
# Wed Jul 29 15:05:21 2020
# +-----------------------------------------------------------------------------+
# | NVIDIA-SMI 450.51.06    Driver Version: 450.51.06    CUDA Version: 11.0     |
# |-------------------------------+----------------------+----------------------+
# | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
# | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
# |                               |                      |               MIG M. |
# |===============================+======================+======================|
# |   0  GeForce GT 730      On   | 00000000:01:00.0 N/A |                  N/A |
# | 40%   37C    P8    N/A /  N/A |      4MiB /  2002MiB |     N/A      Default |
# |                               |                      |                  N/A |
# +-------------------------------+----------------------+----------------------+
# 
# +-----------------------------------------------------------------------------+
# | Processes:                                                                  |
# |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
# |        ID   ID                                                   Usage      |
# |=============================================================================|
# |  No running processes found                                                 |
# +-----------------------------------------------------------------------------+
# 
```

### centos 使用 v2ray 客户端

- 参考: 
  - https://www.v2ray.com/chapter_00/install.html
  - https://toutyrater.github.io/prep/install.html

- 使用 v2ray 一键安装脚本

```sh
bash <(curl -L -s https://install.direct/go.sh)
```

- 手动安装 v2ray 客户端
  - 因为是国内网络, 下载非常非常慢并且经常出错
  - 手动下载 v2ray-core 安装
  - 查看 一键安装脚本的输出或者查看 go.sh 的源文件


```sh
[root@centos ~]# bash <(curl -L -s https://install.direct/go.sh)
Installing V2Ray v4.26.0 on x86_64
Downloading V2Ray: https://github.com/v2fly/v2ray-core/releases/download/v4.26.0/v2ray-linux-64.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   631  100   631    0     0   2160      0 --:--:-- --:--:-- --:--:--  2153
  0 12.2M    0 86602    0     0    806      0  4:25:57  0:01:47  4:24:10     0^C
```


- 从以下网址下载 v2ray 最新版, 
  - https://github.com/v2ray/v2ray-core/releases
  - 如:
  - https://github.com/v2fly/v2ray-core/releases/download/v4.26.0/v2ray-linux-64.zip
  - 上传到一个固定位置, 如 /root/v2ray-linux-64.zip 中
  - 注意, 不能保存到 /tmp/v2ray/v2ray-linux-64.zip 中, 因为 /tmp/v2ray/ 是一键安装脚本使用的临时文件路径, 会出错
  - 使用 --local 参数指定位置

```sh
bash <(curl -L -s https://install.direct/go.sh) --local /root/v2ray-linux-64.zip
```

- 修改 v2ray 配置

```sh
find / -name v2ray

# /etc/v2ray
# /var/log/v2ray
# /usr/bin/v2ray
# /usr/bin/v2ray/v2ray

# 备份配置文件
mv /etc/v2ray/config.json /etc/v2ray/config.json.backup

# 从 windows 中的 v2rayN 中生成 客户端配置文件, 上传到 /etc/v2ray/config.json 中
# 重启 v2ray 服务
systemctl restart v2ray

# 测试 v2ray
curl --socks5 127.0.0.1:10809 http://www.google.com

# 配置 yum 使用 v2ray proxy
echo "proxy=socks5://127.0.0.1:10808/" >> /etc/yum.conf

# 重新 makecache
dnf clean all
dnf mackcache

```

### Configuring YUM to use SOCKS5 proxy

Edit /etc/yum.conf to include the following configuration information in the content:
proxy=socks5://192.168.0.130:1080
proxy_username=username
proxy_password=password



### centos install ffmpeg


- official
  - https://rpmfusion.org/
  - https://rpmfusion.org/Configuration

- install
  - yum localinstall --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm
  - yum localinstall --nogpgcheck  https://download1.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-7.noarch.rpm

- use chinese mirrors
  - https://developer.aliyun.com/mirror/rpmfusion
  - http://mirrors.163.com/rpmfusion/

- reference
  - https://www.inmotionhosting.com/support/edu/live-broadcasting/install-ffmpeg-on-rpm-fusion/
  - https://www.vultr.com/docs/how-to-install-ffmpeg-on-centos
  - https://linuxize.com/post/how-to-install-ffmpeg-on-centos-7/
  - http://li.nux.ro/repos.html
  - https://wiki.centos.org/AdditionalResources/Repositories

```sh
yum install epel-release
# Install RPM Fusion:
yum localinstall --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm
# Install FFmpeg with YUM:
yum install ffmpeg ffmpeg-devel
# Check your FFmpeg version:
ffmpeg -version

```

### redis 只安装 redis-cli

https://stackoverflow.com/questions/21795340/linux-install-redis-cli-only

```bash
cd /usr/local/ && wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable && make redis-cli
cp src/redis-cli /usr/local/bin/

```


#### redis 6.xx make error

```sh
ar crus lib/libjemalloc.a src/jemalloc.o src/arena.o src/background_thread.o src/base.o src/bin.o src/bitmap.o src/ckh.o src/ctl.o src/div.o src/extent.o src/extent_dss.o src/extent_mmap.o src/hash.o src/hooks.o src/large.o src/log.o src/malloc_io.o src/mutex.o src/mutex_pool.o src/nstime.o src/pages.o src/prng.o src/prof.o src/rtree.o src/stats.o src/sz.o src/tcache.o src/ticker.o src/tsd.o src/witness.o
make[3]: Leaving directory `/usr/local/redis-stable/deps/jemalloc'
make[2]: Leaving directory `/usr/local/redis-stable/deps'
    CC anet.o
    CC adlist.o
    CC dict.o
    CC redis-cli.o
    CC zmalloc.o
    CC release.o
    CC ae.o
    CC crcspeed.o
    CC crc64.o
    CC siphash.o
    CC crc16.o
In file included from crc16.c:1:0:
server.h:1051:5: error: expected specifier-qualifier-list before ‘_Atomic’
     _Atomic unsigned int lruclock; /* Clock for LRU eviction */
     ^
make[1]: *** [crc16.o] Error 1
make[1]: Leaving directory `/usr/local/redis-stable/src'
make: *** [redis-cli] Error 2

```

https://www.limstash.com/articles/202005/1633

- 原因
  - 自 redis `6.0.0` 之后，编译 redis 需要支持 `C11` 特性
  - Centos7 默认 gcc 版本为 `4.8.5`，`C11` 特性在 `4.9` 中引入

- 解决方案
  - 安装 [Developer Toolset 7](https://www.softwarecollections.org/en/scls/rhscl/devtoolset-7/) 使用 gcc 7 编译

```bash
yum install centos-release-scl devtoolset-7
```

这不会更新系统原有 gcc 软件包，在当您需要使用 gcc 7 工具链时，使用下列命令进入环境

```bash
scl enable devtoolset-7 bash
```

- 安装

```bash
wget https://github.com/antirez/redis/archive/6.0.1.tar.gz
tar -xvzf 6.0.1.tar.gz
cd redis-6.0.1
./configure
make
make install
```

其他 devtoolset 版本

- [Developer Toolset 8](https://www.softwarecollections.org/en/scls/rhscl/devtoolset-8/)
- [Developer Toolset 9](https://www.softwarecollections.org/en/scls/rhscl/devtoolset-9/)

#### ubuntu 启用root


- 用过ubuntu的人都知道，ubuntu默认root密码是随机的，即每次开机都有一个新的root密码。可以使用 sudo passwd 来修改 root 用户的密码.

```sh
sudo -i
sudo passwd root

disable by below: 
sudo passwd -dl root
```

- 参考: 
  - http://cosminswiki.com/index.php/How_to_let_users_su_without_password
  - http://ag-up.com/?p=457



### 安装 docker-ce

- 参考
  - https://docs.docker.com/install/linux/docker-ce/ubuntu/

```bash
sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88
    
# pub   rsa4096 2017-02-22 [SCEA]
#       9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
# uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
# sub   rsa4096 2017-02-22 [S]

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io

sudo docker run hello-world

```

#### 把 spider 用户添加到 docker 用户组

- 参考
  - https://docs.docker.com/install/linux/linux-postinstall/

```bash
# 添加 docker 用户组
sudo groupadd docker

# 把当前用户添加到 docker 组中, 如
sudo usermod -aG docker spider
# sudo usermod -aG docker $USER
# Log out and log back in so that your group membership is re-evaluated.
# or use the following command to activate the changes to groups:
newgrp docker 

# user spider ssh login and run without sudo
docker run hello-world

```

#### 配置 docker 镜像加速器

您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://dlfa9xic.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 设置 docker 开机启动

Configure Docker to start on boot
Most current Linux distributions (RHEL, CentOS, Fedora, Ubuntu 16.04 and higher) use systemd to manage which services start when the system boots. Ubuntu 14.10 and below use upstart.

systemd

```bash
sudo systemctl enable docker
```

#### docker 创建 scrapy-splash 容器

```bash
docker run -dit --restart=always --name scrapy-splash-1 -p 8050:8050 scrapinghub/splash
# docker run -dit --restart=always --name scrapy-splash-2 -p 8051:8050 scrapinghub/splash

docker run -dit -p 8050:8050 --memory=5.0G --restart=always  --name scrapy-splash-1 scrapinghub/splash --maxrss 4500

```

- docker run  启动一个容器
  * -d 后台启动
  * -p 8050:8050  将容器的8050端口和物理机的8050端口绑定（可以从8050端口访问容器服务应用）
  * –memory=5.0G 容器最大使用内存为5.0GB, 超出这个限制会被主进程杀死（使用free -mg  查看并酌情设置你的内存使用）
  * –restart=always 容器退出后无条件重启（满了5GB被杀死, 然后重启 释放内存）
  * –name splash  容器的名字叫splash（可以忽略）
  * -v ******  三个-v参数是将宿主机的目录挂载进容器, 便于容器能够直接访问挂载目录中的内容
  * scrapinghub/splash:master  用于启动容器的镜像
  * –maxrss 4500 Splash最大内存使用为4500MB

- 测试 splash 服务

  - 在浏览器上访问 http://106.75.60.39:8050, 测试复杂网站渲染
  - 在浏览器上访问 http://106.75.87.150:8050, 测试复杂网站渲染
  - splash 使用教程
    - https://cuiqingcai.com/5638.html
    - https://cuiqingcai.com/?s=splash
    - https://splash.readthedocs.io/en/stable/index.html



## ubuntu 16.04 mysql-server

### 参考

https://blog.csdn.net/itjavawfc/article/details/45567341
https://dev.mysql.com/doc/refman/5.7/en/
https://dev.mysql.com/doc/refman/5.7/en/installing.html


### 准备工作

```bash

# 删除原来的 mysql
sudo apt-get remove --purge mysql-server mysql-client mysql-common
sudo apt-get autoremove
sudo apt-get autoclean

### 删除 mysql 所有相关的文件

find / -name mysql
rm -rf /xxxx/mysql

### 关闭 mysql 所有的进程

ps aux | grep mysql
kill 28892

```

### 添加 mysl apt repository

#### 下载 apt repo 文件
https://dev.mysql.com/downloads/repo/apt/

wget https://dev.mysql.com/get/mysql-apt-config_0.8.14-1_all.deb

#### 安装 repo 文件

dpkg -i mysql-apt-config_0.8.14-1_all.deb

在安装过程中, 会让用户选择 mysql 的版本, 选择 启用的 mysql 的组件, 这里要选择启用 previous (旧版) mysql, 否则会无法安装 mysql 5.7

如果在此时没有启用旧版, 可以使用以下命令重新进行选择

dpkg-reconfigure mysql-apt-config


```bash
# 重建 apt repository 缓存
apt update

# 安装 mysql-server
apt install mysql-server

# 在安装 mysql 的过程中可能会提示输入 root 密码, 这里可以输入, 也可以留空. 
# 留空之后, 在 mysql 安装完成之后, 使用以下命令设置 root 密码
mysql_secure_installation

```

#### 出错时的解决方案

正常情况下, 使用 apt install mysql-server 就能完成 mysql-server 的安装, 
如果出现了以下类似于 AppArmor 的错误, 可以尝试下面的步骤

```
Setting up libevent-core-2.0-5:amd64 (2.0.21-stable-2) ...
Setting up mysql-server-core-5.7 (5.7.16-0ubuntu0.16.04.1) ...
Setting up mysql-server-5.7 (5.7.16-0ubuntu0.16.04.1) ...
update-alternatives: using /etc/mysql/mysql.cnf to provide /etc/mysql/my.cnf (my.cnf) in auto mode
Renaming removed key_buffer and myisam-recover options (if present)
AppArmor parser error for /etc/apparmor.d/usr.sbin.mysqld in /etc/apparmor.d/usr.sbin.mysqld at line 9: Could not open 'abstractions/mysql'
initctl: Unable to connect to Upstart: Failed to connect to socket /com/ubuntu/upstart: Connection refused
insserv: warning: script 'screen-cleanup' missing LSB tags and overrides
insserv: Default-Start undefined, assuming empty start runlevel(s) for script `screen-cleanup'
insserv: Default-Stop undefined, assuming empty stop runlevel(s) for script `screen-cleanup'
Setting up libhtml-template-perl (2.95-2) ...
Setting up mysql-server (5.7.16-0ubuntu0.16.04.1) ...
```

```bash

# 删除安装的 mysql 及所有相关文件

# 参考 https://blog.csdn.net/itjavawfc/article/details/45567341
# 使用以下的步骤安装 mysql

apt update

apt-cache search libaio
apt-get install libaio1

# 安装 mysql-client
apt search mysql-client

# mysql-client-5.7/xenial-updates,xenial-security,now 5.7.28-0ubuntu0.16.04.2 amd64 
#   MySQL database client binaries
# 
# mysql-client-core-5.7/xenial-updates,xenial-security,now 5.7.28-0ubuntu0.16.04.2 amd64 
#   MySQL database core client binaries

apt install mysql-client-core-5.7
apt install mysql-client-5.7

# 安装 mysql-server
apt search mysql-server

# mysql-server-5.7/xenial-updates,xenial-security,now 5.7.28-0ubuntu0.16.04.2 amd64
#   MySQL database server binaries and system database setup
# 
# mysql-server-core-5.7/xenial-updates,xenial-security,now 5.7.28-0ubuntu0.16.04.2 amd64
#   MySQL database server binaries

apt install mysql-server-core-5.7
apt install mysql-server-5.7

```

### 可能会出现的错误

```
2019-12-07T14:20:03.348667Z 0 [Note] mysqld (mysqld 5.7.28-0ubuntu0.16.04.2-log) starting as process 14885 ...
2019-12-07T14:20:03.350371Z 0 [ERROR] You have enabled the binary log, but you haven't provided the mandatory server-id. Please refer to the proper server start-up parameters documentation
2019-12-07T14:20:03.351535Z 0 [ERROR] Aborting
```

解决方法, 修改 mysql-server 配置文件, 取消对 server-id 行的注释

```bash
find / -name my.cnf
# /etc/mysql/my.cnf
# /etc/my.cnf

cat /etc/mysql/my.cnf | grep server-id
# server-id               = 1

vim /etc/mysql/my.cnf

# 再次安装 mysql-server
apt install mysql-server-5.7
```

#### 可能会出现如下错误

```
ERROR: Unable to start MySQL server:
2019-12-07T14:21:59.970928Z 0 [Note] mysqld (mysqld 5.7.28-0ubuntu0.16.04.2-log) starting as process 14995 ...
2019-12-07T14:21:59.987619Z 0 [Note] Plugin 'FEDERATED' is disabled.
2019-12-07T14:21:59.987663Z 0 [ERROR] unknown variable 'log_slow_verbosity=query_plan'
2019-12-07T14:21:59.990527Z 0 [ERROR] Aborting
```

解决方法, 修改 mysql-server 配置文件, 注释掉 log_slow_verbosity=query_plan 这一行

```bash
cat /etc/mysql/my.cnf | grep log_slow_verbosity
# log_slow_verbosity    = query_plan

sed -i 's#log_slow_verbosity#\# log_slow_verbosity#' /etc/mysql/my.cnf

# 再次安装 mysql-server
apt install mysql-server-5.7
```

#### 可能会出现以下错误

```
Dec 07 22:27:14 iZbp1b0x9wrsek2dxkeda8Z mysqld[15416]: 2019-12-07T14:27:14.071214Z 0 [ERROR] Can't start server: Bind on TCP/IP port: Address already in use
Dec 07 22:27:14 iZbp1b0x9wrsek2dxkeda8Z mysqld[15416]: 2019-12-07T14:27:14.071218Z 0 [ERROR] Do you already have another mysqld server running on port: 3306 ?
Dec 07 22:27:14 iZbp1b0x9wrsek2dxkeda8Z mysqld[15416]: 2019-12-07T14:27:14.071229Z 0 [ERROR] Aborting
Dec 07 22:27:14 iZbp1b0x9wrsek2dxkeda8Z mysqld[15416]: 2019-12-07T14:27:14.071244Z 0 [Note] Binlog end
```

解决方法, 关闭所有 mysql 相关的进程

```bash
ps aux | grep mysql

kill 17892
kill 17823

# 重启 mysql
service mysql start

# 尝试使用 localhost 连接

```

### 配置 mysql 远程连接

#### 修改 my.cnf 配置文件, 注释掉 bind

```bash
find / -name my.cnf

# /etc/mysql/my.cnf
# /etc/my.cnf

cat /etc/mysql/my.cnf | grep bind-address
bind-address          = 127.0.0.1

# 修改 my.cnf 文件, 取消掉 bind-address 这一行的注释
# 或者把 ip 地址修改为 0.0.0.0
vim /etc/mysql/my.cnf
# bind-address          = 127.0.0.1

# 重启 mysql-server
service mysql restart
```

为 root 用户进行授权, 使它能够在非 127.0.0.1 的地址上也能访问. 

```bash
mysql -h localhost -P 3306 -p

# 在mysql命令行中运行. 
# grant all privileges获取所有权限, 也可以设置只允许查找的权限. *.*是一个正则表达式, 表示对所有的数据表修改权限. TO 'root'表示对root这个用户赋权限. @'%'表示对所有外部的IP, 也可以指定某个特定的IP. IDENTIFIED BY 'root'是表示密码. 
mysql > GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;

# 刷新权限
mysql > flush privileges;  

```

#### 打开服务器 3306, 3307 端口


iptables -I INPUT -p tcp --dport 8809 -j ACCEPT
iptables -I INPUT -p tcp --dport 20000 -j ACCEPT

参考: https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html



### 可能出现的错误及异常解决

#### Ubuntu 更新 update 源时出现 AppStream cache update completed, but some metadata was ignored due to errors. 

```bash
# 执行
sudo rm /var/lib/dpkg/lock
```

#### spider 用户退格键, 方向键全是乱码

- 原因, 创建用户时没有指定此用户使用的 shell
- 查看 /etc/passwd, 最后一行 `spider:x:1004:1004::/home/spider:/bin/sh`
- 修改为 `spider:x:1004:1004::/home/spider:/bin/bash` 即可


#### pyenv 安装 python zipimport 错误解决

- 参考:
  - https://blog.csdn.net/u014749862/java/article/details/54430022

- 在CentOS以及其他的Linux系统中遇到安装包安装错误的原因, 大多数都是因为缺少依赖包导致的, 
- 对于错误：zipimport.ZipImportError: can’t decompress data, 是因为缺少zlib 的相关工具包导致的

```sh
# 安装zlib相关依赖包：
yum -y install zlib*
```


### 未整理

nmcli 打开网络管理窗口, 添加或修改网络配置
nmtui

service network restart


ip -a


centos 7,8 Wifi 配置, IPv4 Settings > Check "Require IPv4 addressing for this connection to complete"

这样才能正常获取 ipv4 地址

有线和无线, ipv6 都设置为 Ignore.


```
torch.empty(size=(5, 3), dtype=torch.float32, device="cuda:0", requires_grad=False)
/home/spider/.pyenv/versions/ai/lib/python3.6/site-packages/torch/cuda/__init__.py:125: UserWarning: 
GeForce GT 730 with CUDA capability sm_35 is not compatible with the current PyTorch installation.
The current PyTorch install supports CUDA capabilities sm_37 sm_50 sm_60 sm_70 sm_75.
If you want to use the GeForce GT 730 GPU with PyTorch, please check the instructions at https://pytorch.org/get-started/locally/
  warnings.warn(incompatible_device_warn.format(device_name, capability, " ".join(arch_list), device_name))
Traceback (most recent call last):
  File "/home/spider/.pyenv/versions/ai/lib/python3.6/site-packages/IPython/core/formatters.py", line 224, in catch_format_error
    r = method(self, *args, **kwargs)
  File "/home/spider/.pyenv/versions/ai/lib/python3.6/site-packages/IPython/core/formatters.py", line 702, in __call__
    printer.pretty(obj)
  File "/home/spider/.pyenv/versions/ai/lib/python3.6/site-packages/IPython/lib/pretty.py", line 394, in pretty
    return _repr_pprint(obj, self, cycle)
  File "/home/spider/.pyenv/versions/ai/lib/python3.6/site-packages/IPython/lib/pretty.py", line 700, in _repr_pprint


```


#### 修改linux显示的主机名称

永久生效修改
以 CentOS 系统为例，需要更改配置文件生效，修改 /etc/sysconfig/network 里的 HOSTNAME=主机名(可自定义)，重启生效。

如果是 Ubuntu 系统，则需要修改文件 /etc/hostname， 将其对应的主机名修改为新的主机名。

最后，需要将 /etc/hosts 中 127.0.0.1 对应的老主机名更换为新的主机名。

注意：如果是 CentOS 7 操作系统，可以使用命令hostnamectl set-hostname 主机名 来修改，修改完毕后重新 SHELL 登录即可。



#### ubuntu 安装sublime

https://www.sublimetext.com/docs/3/linux_repositories.html


#### ubuntu安装twisted 出错

```sh
# Running setup.py bdist_wheel for Twisted ... error

sudo apt update
sudo apt install python3.6-dev
sudo apt install build-essential python3.6-dev

```

### 再谈 Microsoft Visual C++ 14.0 is required 问题

https://www.hongweipeng.com/index.php/archives/1532/

#### 起步

相信很多 python 开发者在安装一些 C 扩展时候，由于 win 平台缺少编译 C 的环境，经常会报 Visual C++ xxx is required 。今天我安装 hiredis 也遇到了。

![20180615100335.png](dev_env_setup.assets/clip_image002.gif)

#### 用别人编译好了的包

这是其中一种方法，可以现在 https://www.lfd.uci.edu/~gohlke/pythonlibs/ 里面找找有没有与之匹配的，如果有，那么恭喜，问题可以直接解决，下载与本地 python 版本相应的 whl 文件，在通过 pip install xxx.whl 就能成功安装了。

#### Visual Studio

当找不到别人分享的包时，就得找下其他方法了。 如果本地电脑有安装 Visual Studio 软件的话，Visual C++ 都是会附带安装的。

Visual Studio 2013 ---> 12 

Visual Studio 2015 ---> 14 

Visual Studio 2017 ---> 15

 

##### Fix Python 3 on Windows error Microsoft Visual C++ 14.0 is required



Fix the error for Python 3.6 and 3.7 on Windows:

error Microsoft Visual C++ 14.0 is required

 

download Build Tools for Visual Studio 2017

 

https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2017

 

downloaded exe file is:

vs_buildtools__344885804.1522395959.exe

 

downlad offline installer

 

- 参考
  - Install Visual Studio 2017 on low bandwidth or unreliable network environments
  - https://docs.microsoft.com/en-us/visualstudio/install/install-vs-inconsistent-quality-network?view=vs-2017
  - VisualStudio Build Tools 2017 offline installer
  - https://stackoverflow.com/questions/46684230/visualstudio-build-tools-2017-offline-installer
  - Can not install offline ( Unable to download installation files ) 2
  - https://developercommunity.visualstudio.com/content/problem/39981/can-not-install-offline-unable-to-download-install-1.html#reply-54883

vs_buildtools__344885804.1522395959.exe

 --layout c:\VS_BuildTools2017_offline --add Microsoft.VisualStudio.Workload.MSBuildTools --add Microsoft.VisualStudio.Workload.VCTools --lang zh-CN

vs_buildtools__344885804.1522395959.exe --layout C:\vs2017BuildingToolsoffline --lang zh-CN

 

How to install from the offline installation folder

\1.    **Install the certificates** (They are in the "certificates" folder, which is in your Layout folder. **Simply right-click each one to install it**.)

\2.    Run the installation file. For example, run: c:\vs2017offline\vs_enterprise.exe

**Edit:** Remember to install the certs using the Admin account on the PC, or it won't work...

 

 

All steps to install build tools:

\1.   [Download build tools](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=BuildTools)

\2.   Download layout files for offline installation:

·     Possible workloads are specified [here](https://docs.microsoft.com/en-us/visualstudio/install/workload-component-id-vs-build-tools). Following command contains workloads for msbuild and C++ build tools.

·     Run (exe file will have some version numbers in name): vs_BuildTools.exe --layout c:\BT2017offline --add Microsoft.VisualStudio.Workload.MSBuildTools --add Microsoft.VisualStudio.Workload.VCTools --add Microsoft.VisualStudio.Workload.WebBuildTools --add Microsoft.VisualStudio.Workload.NetCoreBuildTools --lang en-US

\3.   Copy c:\BT2017offline to box on which you want to install it.

\4.   Open mmc and import all certificates from c:\BT2017offline\certificates to "Trusted Root Certification Authorities" of **computer** (not current user)

\5.   Run (exe file will have some version numbers in name): c:\BT2017offline\vs_BuildTools.exe --noweb

\6.   Continue clicking next ...

 

 

i'd like to advise to add `--add Microsoft.VisualStudio.Component.NuGet.BuildTools --add Microsoft.Net.Component.4.5.TargetingPack` as [it is listed here](https://developercommunity.visualstudio.com/content/problem/137779/the-getreferencenearesttargetframeworktask-task-wa.html) – [oleksa](https://stackoverflow.com/users/940182/oleksa) [Apr 3 at 14:16](https://stackoverflow.com/questions/46684230/visualstudio-build-tools-2017-offline-installer#comment86271736_47509011)

·     When adding the certs it asks you for a password. I later found out you just leave them blank and continue. – [Robert Brooker](https://stackoverflow.com/users/654654/robert-brooker) [Jun 18 at 22:34](https://stackoverflow.com/questions/46684230/visualstudio-build-tools-2017-offline-installer#comment88836730_47509011)

 

 

Select: Workloads → Visual C++ build tools.

Install options: select only the “Windows 10 SDK” (assuming the computer is Windows 10)

Notes

 

| Python  version                                              | Visual  Studio |
| ------------------------------------------------------------ | -------------- |
| [2.7, 3.4](https://www.scivision.co/python-windows-visual-c++-10-vcvarsall/) | 2010           |
| 3.5                                                          | 2015           |
| 3.6,  3.7                                                    | 2017           |

 

Why Visual C++?

Windows Python needs Visual C++ libraries installed via the SDK to build code, such as via setuptools.extension.Extension or numpy.distutils.core.Extension. For example, building [f2py](https://www.scivision.co/tag/#f2py)modules in Windows with Python requires Visual C++ SDK as installed above. On Linux and Mac, the C++ libraries are installed with the compiler.

Notes

This was the former link to Microsoft Visual C++ Build Tools–no longer active: https://landinghub.visualstudio.com/visual-cpp-build-tools

[Python 3.6 & 3.7](https://bugs.python.org/issue31340) Visual Studio 2017

Python / Visual Studio build matrix (link down) http://vstinner.readthedocs.io/cpython_windows.html

[Visual Studio changed the Build Tools](https://blogs.msdn.microsoft.com/vcblog/2017/11/02/visual-studio-build-tools-now-include-the-vs2017-and-vs2015-msvc-toolsets/) from being C++ specific in late 2017

 

### 移动端爬虫笔记

#### 升级 nox player 中的 adb 的版本

- 问题来源:
  - 在 ubuntu linux 中使用 adb devices 命令查看连接的设备, 会出现如下提示

```
spider@Win10-ZM:/data/spider$ adb devices
adb server version (36) doesn't match this client (41); killing...
* daemon started successfully
List of devices attached
```

- 问题原因:
  - nox player 自带的 adb.exe 的版本与 platform-tools 中 adb.exe 的版本不匹配引起的.


- 解决方法:
  - 使用如下地址下载最新的 windows 版 platform-tools
  - wget https://dl.google.com/android/repository/platform-tools-latest-windows.zip
  - 复制 platform-tools 中的 adb.exe, AdbWinApi.dll, AdbWinUsbApi.dll, 三个文件, 覆盖 Nox\bin 文件夹中的同名文件
  - 重启 nox player



#### app 评论爬虫

```sh
# 创建虚拟环境
pyenv virtualenv 3.6.9 app
# 切换到 root 用户
sudo su - root
# 在根目录下创建目录 /data
mkdir /data
# 把 /data 的所有者和用户组都设置为 spider
chown spider:spider /data
# 退出 root 用户
exit
# 使用 spider 用户创建目录
mkdir /data/spider
cd /data/spider
# 设置 /data/spider 目录的虚拟环境为 app
pyenv local app

```



#### windows 中启动并安装 ubuntu 子系统:

- 启动开发者选项
- windows 启用 "适用于 Linux 的 Windows 子系统"
- windows 应用商店中搜索 Ubuntu 18.04 LTS 并安装
- 启动 Ubuntu 18.04 LTS, 输入用户名和密码, 安装并启动 ubuntu

#### Ubuntu 18.04 LTS 开发环境配置

##### 配置国内 apt 更新源

- 清华源 https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/
- 阿里源 https://developer.aliyun.com/mirror/

```sh
# 备份
cp /etc/apt/sources.list{,.bak}
# 使用 aliyun 源
sed -i 's#http://archive.ubuntu.com/#http://mirrors.aliyun.com/#g' /etc/apt/sources.list
# 更新所有包
apt-get update -y && apt-get upgrade -y
```

