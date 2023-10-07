geek_100114501_k8s_basic

https://kubernetes.io/zh-cn/

操作系统

Ubuntu 22.04 Jammy Jellyfish 桌面版 https://ubuntu.com/download/desktop

https://launchpad.net/ubuntu/+archivemirrors

https://launchpad.net/ubuntu/+cdmirrors

https://ftp.sjtu.edu.cn/ubuntu-cd/22.04.2/

因为 Kubernetes 不是一般的应用软件, 而是一个复杂的系统软件, 对硬件资源的要求有一点高, 好在并不太高, 2 核 CPU, 2G 内存是最低要求, 如果条件允许, 我建议把内存增大到 4G, 硬盘 40G 以上, 这样运行起来会更流畅一些. 另外, 一些对于服务器来说不必要的设备也可以禁用或者删除, 比如声卡, 摄像头, 软驱等等, 可以节约一点系统资源. 

对于 VMWare Fusion, 你需要在 "偏好设置 - 网络"里, 添加一个自定义的网络, 比如这里的 "vmnet3", 网段是 "192.168.10.0", 允许使用 NAT 连接外网, 然后在虚拟机的网络设置里选用这个网络: 

在安装的过程中, 为了节约时间, 建议选择 "最小安装", 同时物理断网, 避免下载升级包. 注意, 断网对于 Apple M1 来说特别重要, 否则 Ubuntu 会自动更新到 5.15 内核, 导致安装后无法正常启动. 

安装完 Linux 系统之后, 我们还要再做一些环境的初始化操作. 

sudo apt update
sudo apt install -y git vim curl jq

Ubuntu 桌面版默认是不支持远程登录的, 所以为了让后续的实验更加便利, 我们还需要安装 "openssh-server", 再使用命令 ip addr , 查看虚拟机的 IP 地址, 然后就可以在宿主机上使用 ssh 命令登录虚拟机: 



| ip              | hostname | hosts           |
| --------------- | -------- | --------------- |
| 192.168.116.150 | server01 | server01.k8s.ts |
| 192.168.116.151 | server02 | server02.k8s.ts |
| 192.168.116.152 | server03 | server03.k8s.ts |
| 192.168.116.153 | worker01 | worker01.k8s.ts |
| 192.168.116.154 | worker02 | worker02.k8s.ts |
| 192.168.116.155 | worker03 | worker03.k8s.ts |



```
192.168.116.150 server01 server01.k8s.ts
192.168.116.151 server01 server01.k8s.ts
192.168.116.152 server01 server01.k8s.ts
192.168.116.153 worker01 worker01.k8s.ts
192.168.116.154 worker02 worker02.k8s.ts
192.168.116.155 worker03 worker03.k8s.ts
```



设置时区

[Ubuntu开启NTP时间同步-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/1141208)

```shell
ubuntu@server01:~$ cat /etc/localtime
TZif2UTCTZif2UTC
UTC0

ubuntu@server01:~$ timedatectl
               Local time: Tue 2023-03-28 11:14:26 UTC
           Universal time: Tue 2023-03-28 11:14:26 UTC
                 RTC time: Tue 2023-03-28 11:14:26
                Time zone: Etc/UTC (UTC, +0000)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no

timedatectl list-timezones
ubuntu@server01:~$ timedatectl list-timezones | grep Shanghai
Asia/Shanghai

sudo timedatectl set-timezone Asia/Shanghai
```





```
$ resolvectl status | grep Current
      Current Scopes: DNS
  Current DNS Server: 192.168.1.1

root@server01:~# resolvectl status | grep Current
    Current Scopes: DNS
Current DNS Server: 192.168.116.2
Current Scopes: none

$ ping -c 5 server01

```

sudo apt install -y openssh-server
ip addr

修改主机名

hostnamectl set-hostname server01

cat /etc/hosts

127.0.0.1  localhost localhost.localdomain localhost4

apt install iputils-ping



10.1.1.11 web01 web01.itcast.cn



```yml
network:
    version: 2
    renderer: networkd
    ethernets:
        eth0:
            addresses:
                - 192.168.1.212/24
            nameservers:
                addresses: [8.8.8.8, 8.8.4.4]
            routes:
                - to: default
                  via: 192.168.1.2


$ sudo vi 00-installer-config.yaml
# This is the network config written by 'subiquity'
network:
  renderer: networkd
  ethernets:
    ens33:
      addresses:
        - 192.168.1.247/24
      nameservers:
        addresses: [4.2.2.2, 8.8.8.8]
      routes:
        - to: default
          via: 192.168.1.1
  version: 2

ubuntu server
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      dhcp4: true
  version: 2

```



master, desktop 22.04.2 minimal install

worker, server 22.04.2 minimal install

servername: ut

user: ubuntu

password: Dm@8481

apt install vim

desktop ip addr

server ip addr

在 vmware 上安装时



apt install open-vm-tools open-vm-tools-dev

配置 ssh 登录

apt install openssh-server

cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

vim /etc/ssh/sshd_config



PermitRootLogin yes-允许root登录, 设为yes. 
PermitRootLogin prohibit-password-允许root登录, 但是禁止root用密码登录, 这行是需要被注释掉的！！！

```shell
# 允许 root 登录
PermitRootLogin yes

# 允许使用 ssh key 验证登录
PubkeyAuthentication yes

```

免密登录

git bash on windows 生成 ssh key

ssh-keygen -t rsa

mkdir ~/.ssh

vim ~/.ssh/authorized_keys

chmod 700 ~/.ssh

chmod 600 ~/.ssh/authorized_keys

service sshd restart



查看当前正在使用的shell

```
➜  ~ echo $SHELL
/bin/zsh
```

查看系统中安装了哪些shell

```
➜  ~ cat /etc/shells
# List of acceptable shells for chpass(1).
# Ftpd will not allow users to connect who are not using
# one of these shells.

/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
```

切换默认shell, 切换到zsh

```
➜  ~ chsh -s /bin/zsh
```

1.23 文档

https://v1-23.docs.kubernetes.io/

https://github.com/kubernetes/sig-release/tree/master/releases

https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md

使用 1.23.17

简单来说, Kubernetes 就是一个生产级别的容器编排平台和集群管理系统, 不仅能够创建, 调度容器, 还能够监控, 管理服务器, 它凝聚了 Google 等大公司和开源社区的集体智慧, 从而让中小型公司也可以具备轻松运维海量计算节点——也就是 "云计算"的能力. 

### Docker

```shell
vmrun.exe snapshot C:\vmware\k8s\server01\server01.vmx 20230329-before-docker
vmrun.exe snapshot C:\vmware\k8s\worker01\worker01.vmx 20230329-before-docker
vmrun.exe snapshot C:\vmware\k8s\worker02\worker02.vmx 20230329-before-docker
vmrun.exe snapshot C:\vmware\k8s\worker03\worker03.vmx 20230329-before-docker

vmrun.exe start C:\vmware\k8s\server01\server01.vmx
vmrun.exe start C:\vmware\k8s\worker01\worker01.vmx
vmrun.exe start C:\vmware\k8s\worker02\worker02.vmx
vmrun.exe start C:\vmware\k8s\worker03\worker03.vmx
```



```shell
vmrun.exe clone C:\vmware\k8s\worker01\worker01.vmx C:\vmware\k8s\server02\server02.vmx full -snapshot=20230329-pull-k8s -cloneName=server02
vmrun.exe clone C:\vmware\k8s\worker01\worker01.vmx C:\vmware\k8s\server03\server03.vmx full -snapshot=20230329-pull-k8s -cloneName=server03
vmrun.exe start C:\vmware\k8s\server02\server02.vmx
```



## minikube

什么是 minikube

Kubernetes 提供了一些快速搭建 Kubernetes 环境的工具, 在官网（https://kubernetes.io/zh/docs/tasks/tools/)上推荐的有两个: kind 和 minikube, 它们都可以在本机上运行完整的 Kubernetes 环境. 

如何搭建 minikube 环境

https://minikube.sigs.k8s.io

### What you’ll need

- 2 CPUs or more
- 2GB of free memory
- 20GB of free disk space
- Internet connection

### Installation

#### Linux

To install the latest minikube **stable** release on **x86-64** **Linux** using **Debian package**:

```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb

# alternative
# sudo install minikube /usr/local/bin/
minikube version
# minikube version: v1.29.0
# commit: ddac20b4b34a9c8c857fc602203b6ba2679794d3

```

#### windows

安装 vmware station

安装 minikube exe package

给 vmware 添加环境变量

使用 vmware driver 启动 minikube

https://minikube.sigs.k8s.io/docs/drivers/vmware/

D:\David\Desktop>minikube start --driver vmware
😄  Microsoft Windows 10 Pro 10.0.19045.2728 Build 19045.2728 上的 minikube v1.29.0
✨  根据现有的配置文件使用 vmware 驱动程序
👍  Starting control plane node minikube in cluster minikube
💾  Downloading Kubernetes v1.26.1 preload ...
    > preloaded-images-k8s-v18-v1...:  397.05 MiB / 397.05 MiB  100.00% 2.19 Mi
🔄  Restarting existing vmware VM for "minikube" ...
🤦  StartHost failed, but will try again: driver start: Machine didn't return an IP after 120 seconds, aborting
🔄  Restarting existing vmware VM for "minikube" ...
😿  Failed to start vmware VM. Running "minikube delete" may fix it: driver start: Machine didn't return an IP after 120 seconds, aborting

❌  Exiting due to GUEST_PROVISION: Failed to start host: driver start: Machine didn't return an IP after 120 seconds, aborting

╭───────────────────────────────────────────────────────────────────────────────────────────╮
│                                                                                           │
│    😿  If the above advice does not help, please let us know:                             │
│    👉  https://github.com/kubernetes/minikube/issues/new/choose                           │
│                                                                                           │
│    Please run `minikube logs --file=logs.txt` and attach logs.txt to the GitHub issue.    │
│                                                                                           │
╰───────────────────────────────────────────────────────────────────────────────────────────╯

### 创建 Kubernetes 实验环境

由于国内网络环境下载 gcr.io 的镜像比较困难, minikube 提供了特殊的启动参数

--image-mirror-country=cn

--registry-mirror=xxx

--image-repository=xxx

minikube start 无法启动 群友建议尝试以下命令解决

minikube delete --all --purge

```shell
minikube start

# 使用某特定的版本
# minikube start --kubernetes-version=v1.23.3

minikube start
* minikube v1.29.0 on Ubuntu 22.04 (amd64)
* Automatically selected the docker driver. Other choices: none, ssh
* Using Docker driver with root privileges
* Starting control plane node minikube in cluster minikube
* Pulling base image ...
* Downloading Kubernetes v1.26.1 preload ...
    > index.docker.io/kicbase/sta...:  407.18 MiB / 407.19 MiB  100.00% 9.67 Mi
    > preloaded-images-k8s-v18-v1...:  331.09 MiB / 397.05 MiB  83.39% 4.10 MiB! minikube was unable to download gcr.io/k8s-minikube/kicbase:v0.0.37, but successfully downloaded docker.io/kicbase/stable:v0.0.37 as a fallback image
    > preloaded-images-k8s-v18-v1...:  397.05 MiB / 397.05 MiB  100.00% 5.38 Mi
* Creating docker container (CPUs=2, Memory=2200MB) ...
* Preparing Kubernetes v1.26.1 on Docker 20.10.23 ...
  - Generating certificates and keys ...
  - Booting up control plane ...
  - Configuring RBAC rules ...
* Configuring bridge CNI (Container Networking Interface) ...
  - Using image gcr.io/k8s-minikube/storage-provisioner:v5
* Verifying Kubernetes components...
* Enabled addons: default-storageclass, storage-provisioner
* kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
* Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
ubuntu@VM-0-23-ubuntu:~$ kubectl
Command 'kubectl' not found, but can be installed with:
sudo snap install kubectl



```



If minikube fails to start with command `minikube start`, see the [drivers page](https://minikube.sigs.k8s.io/docs/drivers/) for help setting up a compatible container or virtual-machine manager.

https://minikube.sigs.k8s.io/docs/drivers/

查看集群的状态

```shell
minikube status
minikube node list
```



Kubernetes 集群里现在只有一个节点, 名字就叫 "minikube", 类型是 "Control Plane", 里面有 host, kubelet, apiserver 三个服务, IP 地址是 192.168.49.2. 

minikube ssh 登录到这个节点上

```shell
ubuntu@VM-0-23-ubuntu:~$ minikube ssh
docker@minikube:~$
docker@minikube:~$ ps
    PID TTY          TIME CMD
  74871 pts/1    00:00:00 bash
  74900 pts/1    00:00:00 ps

docker@minikube:~$ docker images
REPOSITORY                                TAG       IMAGE ID       CREATED         SIZE
nginx                                     alpine    2bc7edbc3cf2   6 weeks ago     40.7MB
registry.k8s.io/kube-apiserver            v1.26.1   deb04688c4a3   2 months ago    134MB
registry.k8s.io/kube-controller-manager   v1.26.1   e9c08e11b07f   2 months ago    124MB
registry.k8s.io/kube-scheduler            v1.26.1   655493523f60   2 months ago    56.3MB
registry.k8s.io/kube-proxy                v1.26.1   46a6bb3c77ce   2 months ago    65.6MB
registry.k8s.io/etcd                      3.5.6-0   fce326961ae2   4 months ago    299MB
registry.k8s.io/pause                     3.9       e6f181688397   5 months ago    744kB
kubernetesui/dashboard                    <none>    07655ddf2eeb   6 months ago    246MB
kubernetesui/metrics-scraper              <none>    115053965e86   10 months ago   43.8MB
registry.k8s.io/coredns/coredns           v1.9.3    5185b96f0bec   10 months ago   48.8MB
registry.k8s.io/pause                     3.6       6270bb605e12   19 months ago   683kB
gcr.io/k8s-minikube/storage-provisioner   v5        6e38f40d628d   24 months ago   31.5MB

docker@minikube:~$ docker ps -a
CONTAINER ID   IMAGE                          COMMAND                  CREATED          STATUS                   PORTS     NAMES
e43737535cbc   kubernetesui/dashboard         "/dashboard --insecu…"   52 minutes ago   Up 52 minutes                      k8s_kubernetes-dashboard_kubernetes-dashboard-55c4cbbc7c-w7g7b_kubernetes-dashboard_4cf72413-4380-4f44-b8e9-747d4732e591_0
4cd6398a1a4b   kubernetesui/metrics-scraper   "/metrics-sidecar"       52 minutes ago   Up 52 minutes                      k8s_dashboard-metrics-scraper_dashboard-metrics-scraper-5c6664855-z2xsd_kubernetes-dashboard_b8960149-b03c-4eb7-8f16-8c86c510b1e7_0
cae50c92be20   registry.k8s.io/pause:3.6      "/pause"                 52 minutes ago   Up 52 minutes                      k8s_POD_dashboard-metrics-scraper-5c6664855-z2xsd_kubernetes-dashboard_b8960149-b03c-4eb7-8f16-8c86c510b1e7_0
4d7b3034fbac   registry.k8s.io/pause:3.6      "/pause"                 52 minutes ago   Up 52 minutes                      k8s_POD_kubernetes-dashboard-55c4cbbc7c-w7g7b_kubernetes-dashboard_4cf72413-4380-4f44-b8e9-747d4732e591_0
45c324a035ba   nginx                          "/docker-entrypoint.…"   56 minutes ago   Up 56 minutes                      k8s_ngx_ngx_default_24a0addd-b436-4c25-aa5b-d8456c90f7b8_0
4982c3217931   registry.k8s.io/pause:3.6      "/pause"                 56 minutes ago   Up 56 minutes                      k8s_POD_ngx_default_24a0addd-b436-4c25-aa5b-d8456c90f7b8_0
e014878ced05   6e38f40d628d                   "/storage-provisioner"   2 hours ago      Up 2 hours                         k8s_storage-provisioner_storage-provisioner_kube-system_ddd42ef6-8056-4f16-89bb-c03dbc379d24_1
f1f7c434ec3e   5185b96f0bec                   "/coredns -conf /etc…"   2 hours ago      Up 2 hours                         k8s_coredns_coredns-787d4945fb-jh8mm_kube-system_150ab818-bc7e-4bf2-901f-f77e0f39366c_0
f6929c3ee52f   registry.k8s.io/pause:3.6      "/pause"                 2 hours ago      Up 2 hours                         k8s_POD_coredns-787d4945fb-jh8mm_kube-system_150ab818-bc7e-4bf2-901f-f77e0f39366c_0
fa93f139f97d   46a6bb3c77ce                   "/usr/local/bin/kube…"   2 hours ago      Up 2 hours                         k8s_kube-proxy_kube-proxy-cxv9r_kube-system_44599790-45ff-4530-83da-98eb1814c84f_0
84cfd1435367   registry.k8s.io/pause:3.6      "/pause"                 2 hours ago      Up 2 hours                         k8s_POD_kube-proxy-cxv9r_kube-system_44599790-45ff-4530-83da-98eb1814c84f_0
7476782cf959   6e38f40d628d                   "/storage-provisioner"   2 hours ago      Exited (1) 2 hours ago             k8s_storage-provisioner_storage-provisioner_kube-system_ddd42ef6-8056-4f16-89bb-c03dbc379d24_0
c3df2d2e0ff6   registry.k8s.io/pause:3.6      "/pause"                 2 hours ago      Up 2 hours                         k8s_POD_storage-provisioner_kube-system_ddd42ef6-8056-4f16-89bb-c03dbc379d24_0
22abe45a714b   deb04688c4a3                   "kube-apiserver --ad…"   2 hours ago      Up 2 hours                         k8s_kube-apiserver_kube-apiserver-minikube_kube-system_5239bb256c1be9f71fd10c884d9299b1_0
a449fadae57a   fce326961ae2                   "etcd --advertise-cl…"   2 hours ago      Up 2 hours                         k8s_etcd_etcd-minikube_kube-system_a121e106627e5c6efa9ba48006cc43bf_0
04407e84a212   e9c08e11b07f                   "kube-controller-man…"   2 hours ago      Up 2 hours                         k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_5175bba984ed52052d891b5a45b584b6_0
191b52eef416   655493523f60                   "kube-scheduler --au…"   2 hours ago      Up 2 hours                         k8s_kube-scheduler_kube-scheduler-minikube_kube-system_197cd0de602d7cb722d0bd2daf878121_0
276d136a25d1   registry.k8s.io/pause:3.6      "/pause"                 2 hours ago      Up 2 hours                         k8s_POD_kube-scheduler-minikube_kube-system_197cd0de602d7cb722d0bd2daf878121_0
ddf8e8342aa9   registry.k8s.io/pause:3.6      "/pause"                 2 hours ago      Up 2 hours                         k8s_POD_etcd-minikube_kube-system_a121e106627e5c6efa9ba48006cc43bf_0
a1b6056bf379   registry.k8s.io/pause:3.6      "/pause"                 2 hours ago      Up 2 hours                         k8s_POD_kube-apiserver-minikube_kube-system_5239bb256c1be9f71fd10c884d9299b1_0
59a21faca399   registry.k8s.io/pause:3.6      "/pause"                 2 hours ago      Up 2 hours                         k8s_POD_kube-controller-manager-minikube_kube-system_5175bba984ed52052d891b5a45b584b6_0

```



Pause Kubernetes without impacting deployed applications:

```shell
minikube pause
```

Unpause a paused instance:

```shell
minikube unpause
```

Halt the cluster:

```shell
minikube stop
```

Change the default memory limit (requires a restart):

```shell
minikube config set memory 9001
```

Browse the catalog of easily installed Kubernetes services:

```shell
minikube addons list
```

Create a second cluster running an older Kubernetes release:

```shell
minikube start -p aged --kubernetes-version=v1.16.1
```

Delete all of the minikube clusters:

```shell
minikube delete --all
```

## kubectl

minikube 只能够搭建 Kubernetes 环境, 要操作 Kubernetes, 还需要另一个专门的客户端工具 "kubectl"

### 自动安装 kubectl

kubectl 是一个与 Kubernetes, minikube 彼此独立的项目, 所以不包含在 minikube 里, 但 minikube 提供了安装它的简化方式, 你只需执行下面的这条命令: 

```shell
minikube kubectl
```

它就会把与当前 Kubernetes 版本匹配的 kubectl 下载下来, 存放在内部目录（例如 .minikube/cache/linux/arm64/v1.23.3), 然后我们就可以使用它来对 Kubernetes "发号施令"了. 

```
find / -name kubectl
/var/lib/docker/volumes/minikube/_data/lib/minikube/binaries/v1.26.1/kubectl

ubuntu@VM-0-23-ubuntu:~$ alias kubectl="minikube kubectl --"
ubuntu@VM-0-23-ubuntu:~$ kubectl
    > kubectl.sha256:  64 B / 64 B [-------------------------] 100.00% ? p/s 0s
    > kubectl:  45.80 MiB / 45.80 MiB [------------] 100.00% 16.28 MiB p/s 3.0s
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/

Basic Commands (Beginner):
  create          Create a resource from a file or from stdin
  expose          Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run             Run a particular image on the cluster
  set             Set specific features on objects

Basic Commands (Intermediate):
  explain         Get documentation for a resource
  get             Display one or many resources
  edit            Edit a resource on the server
  delete          Delete resources by file names, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout         Manage the rollout of a resource
  scale           Set a new size for a deployment, replica set, or replication controller
  autoscale       Auto-scale a deployment, replica set, stateful set, or replication controller

Cluster Management Commands:
  certificate     Modify certificate resources.
  cluster-info    Display cluster information
  top             Display resource (CPU/memory) usage
  cordon          Mark node as unschedulable
  uncordon        Mark node as schedulable
  drain           Drain node in preparation for maintenance
  taint           Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe        Show details of a specific resource or group of resources
  logs            Print the logs for a container in a pod
  attach          Attach to a running container
  exec            Execute a command in a container
  port-forward    Forward one or more local ports to a pod
  proxy           Run a proxy to the Kubernetes API server
  cp              Copy files and directories to and from containers
  auth            Inspect authorization
  debug           Create debugging sessions for troubleshooting workloads and nodes
  events          List events

Advanced Commands:
  diff            Diff the live version against a would-be applied version
  apply           Apply a configuration to a resource by file name or stdin
  patch           Update fields of a resource
  replace         Replace a resource by file name or stdin
  wait            Experimental: Wait for a specific condition on one or many resources
  kustomize       Build a kustomization target from a directory or URL.

Settings Commands:
  label           Update the labels on a resource
  annotate        Update the annotations on a resource
  completion      Output shell completion code for the specified shell (bash, zsh, fish, or powershell)

Other Commands:
  alpha           Commands for features in alpha
  api-resources   Print the supported API resources on the server
  api-versions    Print the supported API versions on the server, in the form of "group/version"
  config          Modify kubeconfig files
  plugin          Provides utilities for interacting with plugins
  version         Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).


```

### 手动安装 kubectl

https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux

```bash
# 安装最新 stable 版本
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
# 安装指定版本
curl -LO https://dl.k8s.io/release/v1.26.0/bin/linux/amd64/kubectl

```

### kubctl 命令

```shell
ubuntu@VM-0-23-ubuntu:~$ kubectl version
WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short.  Use --output=yaml|json to get the full version.
Client Version: version.Info{Major:"1", Minor:"26", GitVersion:"v1.26.1", GitCommit:"8f94681cd294aa8cfd3407b8191f6c70214973a4", GitTreeState:"clean", BuildDate:"2023-01-18T15:58:16Z", GoVersion:"go1.19.5", Compiler:"gc", Platform:"linux/amd64"}
Kustomize Version: v4.5.7
Server Version: version.Info{Major:"1", Minor:"26", GitVersion:"v1.26.1", GitCommit:"8f94681cd294aa8cfd3407b8191f6c70214973a4", GitTreeState:"clean", BuildDate:"2023-01-18T15:51:25Z", GoVersion:"go1.19.5", Compiler:"gc", Platform:"linux/amd64"}

ubuntu@VM-0-23-ubuntu:~$ kubectl version --output=yaml
clientVersion:
  buildDate: "2023-01-18T15:58:16Z"
  compiler: gc
  gitCommit: 8f94681cd294aa8cfd3407b8191f6c70214973a4
  gitTreeState: clean
  gitVersion: v1.26.1
  goVersion: go1.19.5
  major: "1"
  minor: "26"
  platform: linux/amd64
kustomizeVersion: v4.5.7
serverVersion:
  buildDate: "2023-01-18T15:51:25Z"
  compiler: gc
  gitCommit: 8f94681cd294aa8cfd3407b8191f6c70214973a4
  gitTreeState: clean
  gitVersion: v1.26.1
  goVersion: go1.19.5
  major: "1"
  minor: "26"
  platform: linux/amd64

ubuntu@VM-0-23-ubuntu:~$ kubectl version --output=json
{
  "clientVersion": {
    "major": "1",
    "minor": "26",
    "gitVersion": "v1.26.1",
    "gitCommit": "8f94681cd294aa8cfd3407b8191f6c70214973a4",
    "gitTreeState": "clean",
    "buildDate": "2023-01-18T15:58:16Z",
    "goVersion": "go1.19.5",
    "compiler": "gc",
    "platform": "linux/amd64"
  },
  "kustomizeVersion": "v4.5.7",
  "serverVersion": {
    "major": "1",
    "minor": "26",
    "gitVersion": "v1.26.1",
    "gitCommit": "8f94681cd294aa8cfd3407b8191f6c70214973a4",
    "gitTreeState": "clean",
    "buildDate": "2023-01-18T15:51:25Z",
    "goVersion": "go1.19.5",
    "compiler": "gc",
    "platform": "linux/amd64"
  }
}

```



### kubectl 命令自动补全

https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#optional-kubectl-configurations-and-plugins

https://github.com/scop/bash-completion#installation

You now need to ensure that the kubectl completion script gets sourced in all your shell sessions. There are two ways in which you can do this:

```bash
echo 'source <(kubectl completion bash)' >>~/.bashrc
```

If you have an alias for kubectl, you can extend shell completion to work with that alias:

```bash
echo 'alias k=kubectl' >>~/.bashrc
echo 'complete -o default -F __start_kubectl k' >>~/.bashrc
```

**Note:** bash-completion sources all completion scripts in `/etc/bash_completion.d`.

#### Kubernetes 里运行一个 Nginx 应用

下面我们在 Kubernetes 里运行一个 Nginx 应用, 命令与 Docker 一样, 也是 run, 不过形式上有点区别, 需要用 --image 指定镜像, 然后 Kubernetes 会自动拉取并运行: 

```shell

ubuntu@VM-0-23-ubuntu:~$ kubectl run ngx --image=nginx:alpine
pod/ngx created
ubuntu@VM-0-23-ubuntu:~$ kubectl get pod
NAME   READY   STATUS    RESTARTS   AGE
ngx    1/1     Running   0          10s

ubuntu@VM-0-23-ubuntu:~$ kubectl get po -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
default       ngx                                1/1     Running   0             3m44s
kube-system   coredns-787d4945fb-jh8mm           1/1     Running   0             52m
kube-system   etcd-minikube                      1/1     Running   0             52m
kube-system   kube-apiserver-minikube            1/1     Running   0             52m
kube-system   kube-controller-manager-minikube   1/1     Running   0             52m
kube-system   kube-proxy-cxv9r                   1/1     Running   0             52m
kube-system   kube-scheduler-minikube            1/1     Running   0             52m
kube-system   storage-provisioner                1/1     Running   1 (51m ago)   52m

```

注: READY 显示的是 Pod 内部的容器状态, 格式是 x/y, 表示 Pod 中一共定义了 y 个容器, 其中 x 个是正常的 ready

### Kubernetes 架构和组件介绍

Kubernetes 采用了现今流行的 "控制面 / 数据面"（Control Plane / Data Plane)架构, 集群里的计算机被称为 "节点"（Node), 可以是实机也可以是虚机, 少量的节点用作控制面来执行集群的管理维护工作, 其他的大部分节点都被划归数据面, 用来跑业务应用. 

控制面的节点在 Kubernetes 里叫做 Master Node, 一般简称为 Master, 它是整个集群里最重要的部分, 可以说是 Kubernetes 的大脑和心脏. 

数据面的节点叫做 Worker Node, 一般就简称为 Worker 或者 Node, 相当于 Kubernetes 的手和脚, 在 Master 的指挥下干活. 

查看 Kubernetes 的节点状态

```shell
kubectl get node
ubuntu@VM-0-23-ubuntu:~$ kubectl get node
NAME       STATUS   ROLES           AGE    VERSION
minikube   Ready    control-plane   114m   v1.26.1

```

Master 和 Node 的划分不是绝对的. 当集群的规模较小, 工作负载较少的时候, Master 也可以承担 Node 的工作, 就像我们搭建的 minikube 环境, 它就只有一个节点, 这个节点既是 Master 又是 Node. 

#### Master 里的组件有哪些

为了确保控制面的高可用, Kubernetes 集群里都会部署多个 Master 节点, 数量一般会是奇数 3/5/7

Master 里有 4 个组件, 分别是 apiserver, etcd, scheduler, controller-manager. 

- apiserver 是 Master 节点——同时也是整个 Kubernetes 系统的唯一入口, 它对外公开了一系列的 RESTful API, 并且加上了验证, 授权等功能, 所有其他组件都只能和它直接通信, 可以说是 Kubernetes 里的联络员. 

- etcd 是一个高可用的分布式 Key-Value 数据库, 用来持久化存储系统里的各种资源对象和状态, 相当于 Kubernetes 里的配置管理员. 注意它只与 apiserver 有直接联系, 也就是说任何其他组件想要读写 etcd 里的数据都必须经过 apiserver. 

- scheduler 负责容器的编排工作, 检查节点的资源状态, 把 Pod 调度到最适合的节点上运行, 相当于部署人员. 因为节点状态和 Pod 信息都存储在 etcd 里, 所以 scheduler 必须通过 apiserver 才能获得. 

- controller-manager 负责维护容器和节点等资源的状态, 实现故障检测, 服务迁移, 应用伸缩等功能, 相当于监控运维人员. 同样地, 它也必须通过 apiserver 获得存储在 etcd 里的信息, 才能够实现对资源的各种操作. 
  - controller-manager 是很多个 controller 的集合体, 每一个 controller 负责一种控制循环, 如 node controller, namespace controller, 但为了简体被合并到一个进程中执行了

这 4 个组件也都被容器化了, 运行在集群的 Pod 里, 我们可以用 kubectl 来查看它们的状态, 使用命令: 

```shell

kubectl get pod -n kube-system
# -n kube-system 参数, 表示检查 "kube-system"名字空间里的 Pod

ubuntu@VM-0-23-ubuntu:~$ kubectl get pod -n kube-system
NAME                               READY   STATUS    RESTARTS       AGE
coredns-787d4945fb-jh8mm           1/1     Running   0              116m
etcd-minikube                      1/1     Running   0              116m
kube-apiserver-minikube            1/1     Running   0              116m
kube-controller-manager-minikube   1/1     Running   0              116m
kube-proxy-cxv9r                   1/1     Running   0              116m
kube-scheduler-minikube            1/1     Running   0              116m
storage-provisioner                1/1     Running   1 (115m ago)   116m

```

#### Node 里的组件有哪些

这就需要 Node 里的 3 个组件了, 分别是 kubelet, kube-proxy, container-runtime. 

- kubelet 是 Node 的代理, 负责管理 Node 相关的绝大部分操作, Node 上只有它能够与 apiserver 通信, 实现状态报告, 命令下发, 启停容器等功能, 相当于是 Node 上的一个 "小管家". 

- kube-proxy 的作用有点特别, 它是 Node 的网络代理, 只负责管理容器的网络通信, 简单来说就是为 Pod 转发 TCP/UDP 数据包, 相当于是专职的 "小邮差". 

- 第三个组件 container-runtime 我们就比较熟悉了, 它是容器和镜像的实际使用者, 在 kubelet 的指挥下创建容器, 管理 Pod 的生命周期, 是真正干活的 "苦力". 

这 3 个组件中只有 kube-proxy 被容器化了, 而 kubelet 因为必须要管理整个节点, 容器化会限制它的能力, 所以它必须在 container-runtime 之外运行. 

使用 minikube ssh 命令登录到节点后, 可以用 docker ps 看到 kube-proxy: 

```shell
minikube ssh
docker ps |grep kube-proxy

ubuntu@VM-0-23-ubuntu:~$ minikube ssh
Last login: Mon Mar 27 02:29:39 2023 from 192.168.49.1
docker@minikube:~$ docker ps |grep kube-proxy
fa93f139f97d   46a6bb3c77ce                   "/usr/local/bin/kube…"   2 hours ago         Up 2 hours                   k8s_kube-proxy_kube-proxy-cxv9r_kube-system_44599790-45ff-4530-83da-98eb1814c84f_0
84cfd1435367   registry.k8s.io/pause:3.6      "/pause"                 2 hours ago         Up 2 hours                   k8s_POD_kube-proxy-cxv9r_kube-system_44599790-45ff-4530-83da-98eb1814c84f_0

uname -a  #显示是Ubuntu操作系统
docker version #这个节点里也跑了一个docker, 但其实是复用了宿主机的docker
docker ps    #能够看到节点里以容器形式运行的Kubernetes进程, 比如pause, scheduler等等
exit

```

而 kubelet 用 docker ps 是找不到的, 需要用操作系统的 ps 命令: 

```shell
ps -ef|grep kubelet

docker@minikube:~$ ps -ef|grep kubelet
root        1888    1827  3 00:44 ?        00:03:47 kube-apiserver --advertise-address=192.168.49.2 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/var/lib/minikube/certs/ca.crt --enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota --enable-bootstrap-token-auth=true --etcd-cafile=/var/lib/minikube/certs/etcd/ca.crt --etcd-certfile=/var/lib/minikube/certs/apiserver-etcd-client.crt --etcd-keyfile=/var/lib/minikube/certs/apiserver-etcd-client.key --etcd-servers=https://127.0.0.1:2379 --kubelet-client-certificate=/var/lib/minikube/certs/apiserver-kubelet-client.crt --kubelet-client-key=/var/lib/minikube/certs/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/var/lib/minikube/certs/front-proxy-client.crt --proxy-client-key-file=/var/lib/minikube/certs/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/var/lib/minikube/certs/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=8443 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/minikube/certs/sa.pub --service-account-signing-key-file=/var/lib/minikube/certs/sa.key --service-cluster-ip-range=10.96.0.0/12 --tls-cert-file=/var/lib/minikube/certs/apiserver.crt --tls-private-key-file=/var/lib/minikube/certs/apiserver.key
root        2089       1  1 00:44 ?        00:01:55 /var/lib/minikube/binaries/v1.26.1/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime=remote --container-runtime-endpoint=/var/run/cri-dockerd.sock --hostname-override=minikube --image-service-endpoint=/var/run/cri-dockerd.sock --kubeconfig=/etc/kubernetes/kubelet.conf --node-ip=192.168.49.2
```

这些 pod 与使用 `minikube ssh` 命令进入 minikube system 中使用 `docker ps -a` 和 `docker images` 得到的容器和镜像是对应的

#### Kubernetes 的大致工作流程

把 Node 里的组件和 Master 里的组件放在一起来看, 就能够明白 Kubernetes 的大致工作流程了: 

每个 Node 上的 kubelet 会定期向 apiserver 上报节点状态, apiserver 再存到 etcd 里. 

每个 Node 上的 kube-proxy 实现了 TCP/UDP 反向代理, 让容器对外提供稳定的服务. 

scheduler 通过 apiserver 得到当前的节点状态, 调度 Pod, 然后 apiserver 下发命令给某个 Node 的 kubelet, kubelet 调用 container-runtime 启动容器. 

controller-manager 也通过 apiserver 得到实时的节点状态, 监控可能的异常情况, 再使用相应的手段去调节恢复. 

#### Kubernetes 插件 Addon

```
minikube addons list

ubuntu@VM-0-23-ubuntu:~$ minikube addons list
|-----------------------------|----------|--------------|--------------------------------|
|         ADDON NAME          | PROFILE  |    STATUS    |           MAINTAINER           |
|-----------------------------|----------|--------------|--------------------------------|
| ambassador                  | minikube | disabled     | 3rd party (Ambassador)         |
| auto-pause                  | minikube | disabled     | Google                         |
| cloud-spanner               | minikube | disabled     | Google                         |
| csi-hostpath-driver         | minikube | disabled     | Kubernetes                     |
| dashboard                   | minikube | enabled ✅   | Kubernetes                     |
| default-storageclass        | minikube | enabled ✅   | Kubernetes                     |
| efk                         | minikube | disabled     | 3rd party (Elastic)            |
| freshpod                    | minikube | disabled     | Google                         |
| gcp-auth                    | minikube | disabled     | Google                         |
| gvisor                      | minikube | disabled     | Google                         |
| headlamp                    | minikube | disabled     | 3rd party (kinvolk.io)         |
| helm-tiller                 | minikube | disabled     | 3rd party (Helm)               |
| inaccel                     | minikube | disabled     | 3rd party (InAccel             |
|                             |          |              | [info@inaccel.com])            |
| ingress                     | minikube | disabled     | Kubernetes                     |
| ingress-dns                 | minikube | disabled     | Google                         |
| istio                       | minikube | disabled     | 3rd party (Istio)              |
| istio-provisioner           | minikube | disabled     | 3rd party (Istio)              |
| kong                        | minikube | disabled     | 3rd party (Kong HQ)            |
| kubevirt                    | minikube | disabled     | 3rd party (KubeVirt)           |
| logviewer                   | minikube | disabled     | 3rd party (unknown)            |
| metallb                     | minikube | disabled     | 3rd party (MetalLB)            |
| metrics-server              | minikube | disabled     | Kubernetes                     |
| nvidia-driver-installer     | minikube | disabled     | Google                         |
| nvidia-gpu-device-plugin    | minikube | disabled     | 3rd party (Nvidia)             |
| olm                         | minikube | disabled     | 3rd party (Operator Framework) |
| pod-security-policy         | minikube | disabled     | 3rd party (unknown)            |
| portainer                   | minikube | disabled     | 3rd party (Portainer.io)       |
| registry                    | minikube | disabled     | Google                         |
| registry-aliases            | minikube | disabled     | 3rd party (unknown)            |
| registry-creds              | minikube | disabled     | 3rd party (UPMC Enterprises)   |
| storage-provisioner         | minikube | enabled ✅   | Google                         |
| storage-provisioner-gluster | minikube | disabled     | 3rd party (Gluster)            |
| volumesnapshots             | minikube | disabled     | Kubernetes                     |
|-----------------------------|----------|--------------|--------------------------------|

```

### Kubernetes API 对象

https://kubernetes.io/docs/reference/kubernetes-api/

因为 apiserver 是 Kubernetes 系统的唯一入口, 外部用户和内部组件都必须和它通信, 而它采用了 HTTP 协议的 URL 资源理念, API 风格也用 RESTful 的 GET/POST/DELETE 等等, 所以, 这些概念很自然地就被称为是 "API 对象"了. 

kubectl api-resources 来查看当前 Kubernetes 版本支持的所有对象

因为 kubernetes 的开发语言是 Go, API 对象的命名遵循 Camel Case 风格

```shell

ubuntu@VM-0-23-ubuntu:~$ kubectl api-resources
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
bindings                                       v1                                     true         Binding
componentstatuses                 cs           v1                                     false        ComponentStatus
configmaps                        cm           v1                                     true         ConfigMap
endpoints                         ep           v1                                     true         Endpoints
events                            ev           v1                                     true         Event
limitranges                       limits       v1                                     true         LimitRange
namespaces                        ns           v1                                     false        Namespace
nodes                             no           v1                                     false        Node
persistentvolumeclaims            pvc          v1                                     true         PersistentVolumeClaim
persistentvolumes                 pv           v1                                     false        PersistentVolume
pods                              po           v1                                     true         Pod
podtemplates                                   v1                                     true         PodTemplate
replicationcontrollers            rc           v1                                     true         ReplicationController
resourcequotas                    quota        v1                                     true         ResourceQuota
secrets                                        v1                                     true         Secret
serviceaccounts                   sa           v1                                     true         ServiceAccount
services                          svc          v1                                     true         Service
mutatingwebhookconfigurations                  admissionregistration.k8s.io/v1        false        MutatingWebhookConfiguration
validatingwebhookconfigurations                admissionregistration.k8s.io/v1        false        ValidatingWebhookConfiguration
customresourcedefinitions         crd,crds     apiextensions.k8s.io/v1                false        CustomResourceDefinition
apiservices                                    apiregistration.k8s.io/v1              false        APIService
controllerrevisions                            apps/v1                                true         ControllerRevision
daemonsets                        ds           apps/v1                                true         DaemonSet
deployments                       deploy       apps/v1                                true         Deployment
replicasets                       rs           apps/v1                                true         ReplicaSet
statefulsets                      sts          apps/v1                                true         StatefulSet
tokenreviews                                   authentication.k8s.io/v1               false        TokenReview
localsubjectaccessreviews                      authorization.k8s.io/v1                true         LocalSubjectAccessReview
selfsubjectaccessreviews                       authorization.k8s.io/v1                false        SelfSubjectAccessReview
selfsubjectrulesreviews                        authorization.k8s.io/v1                false        SelfSubjectRulesReview
subjectaccessreviews                           authorization.k8s.io/v1                false        SubjectAccessReview
horizontalpodautoscalers          hpa          autoscaling/v2                         true         HorizontalPodAutoscaler
cronjobs                          cj           batch/v1                               true         CronJob
jobs                                           batch/v1                               true         Job
certificatesigningrequests        csr          certificates.k8s.io/v1                 false        CertificateSigningRequest
leases                                         coordination.k8s.io/v1                 true         Lease
endpointslices                                 discovery.k8s.io/v1                    true         EndpointSlice
events                            ev           events.k8s.io/v1                       true         Event
flowschemas                                    flowcontrol.apiserver.k8s.io/v1beta3   false        FlowSchema
prioritylevelconfigurations                    flowcontrol.apiserver.k8s.io/v1beta3   false        PriorityLevelConfiguration
ingressclasses                                 networking.k8s.io/v1                   false        IngressClass
ingresses                         ing          networking.k8s.io/v1                   true         Ingress
networkpolicies                   netpol       networking.k8s.io/v1                   true         NetworkPolicy
runtimeclasses                                 node.k8s.io/v1                         false        RuntimeClass
poddisruptionbudgets              pdb          policy/v1                              true         PodDisruptionBudget
clusterrolebindings                            rbac.authorization.k8s.io/v1           false        ClusterRoleBinding
clusterroles                                   rbac.authorization.k8s.io/v1           false        ClusterRole
rolebindings                                   rbac.authorization.k8s.io/v1           true         RoleBinding
roles                                          rbac.authorization.k8s.io/v1           true         Role
priorityclasses                   pc           scheduling.k8s.io/v1                   false        PriorityClass
csidrivers                                     storage.k8s.io/v1                      false        CSIDriver
csinodes                                       storage.k8s.io/v1                      false        CSINode
csistoragecapacities                           storage.k8s.io/v1                      true         CSIStorageCapacity
storageclasses                    sc           storage.k8s.io/v1                      false        StorageClass
volumeattachments                              storage.k8s.io/v1                      false        VolumeAttachment

```

kubectl 命令的时候, 你还可以加上一个参数 --v=9, 它会显示出详细的命令执行过程, 清楚地看到发出的 HTTP 请求, 比如: 

kubectl get pod --v=9

```
ubuntu@VM-0-23-ubuntu:~$ kubectl get pod --v=9
I0327 11:19:29.843772 1122373 loader.go:373] Config loaded from file:  /home/ubuntu/.kube/config
I0327 11:19:29.844495 1122373 cert_rotation.go:137] Starting client certificate rotation controller
I0327 11:19:29.849431 1122373 round_trippers.go:466] curl -v -XGET  -H "Accept: application/json;as=Table;v=v1;g=meta.k8s.io,application/json;as=Table;v=v1beta1;g=meta.k8s.io,application/json" -H "User-Agent: kubectl/v1.26.1 (linux/amd64) kubernetes/8f94681" 'https://192.168.49.2:8443/api/v1/namespaces/default/pods?limit=500'
I0327 11:19:29.849723 1122373 round_trippers.go:510] HTTP Trace: Dial to tcp:192.168.49.2:8443 succeed
I0327 11:19:29.856230 1122373 round_trippers.go:553] GET https://192.168.49.2:8443/api/v1/namespaces/default/pods?limit=500 200 OK in 6 milliseconds
I0327 11:19:29.856278 1122373 round_trippers.go:570] HTTP Statistics: DNSLookup 0 ms Dial 0 ms TLSHandshake 4 ms ServerProcessing 2 ms Duration 6 ms
I0327 11:19:29.856294 1122373 round_trippers.go:577] Response Headers:
I0327 11:19:29.856310 1122373 round_trippers.go:580]     X-Kubernetes-Pf-Prioritylevel-Uid: 6e762326-699f-4a84-aa81-6347b350db0e
I0327 11:19:29.856325 1122373 round_trippers.go:580]     Date: Mon, 27 Mar 2023 03:19:29 GMT
I0327 11:19:29.856343 1122373 round_trippers.go:580]     Audit-Id: cd5ec4f2-db88-4e0f-bd54-3ca295643d39
I0327 11:19:29.856361 1122373 round_trippers.go:580]     Cache-Control: no-cache, private
I0327 11:19:29.856377 1122373 round_trippers.go:580]     Content-Type: application/json
I0327 11:19:29.856397 1122373 round_trippers.go:580]     X-Kubernetes-Pf-Flowschema-Uid: c14f8e80-8525-4286-bdd5-07e3e589784c
I0327 11:19:29.856518 1122373 request.go:1171] Response Body: {"kind":"Table","apiVersion":"meta.k8s.io/v1","metadata":{"resourceVersion":"7905"},"columnDefinitions":[{"name":"Name","type":"string","format":"name","description":"Name must be unique within a namespace. Is required when creating resources, although some resources may allow a client to request the generation of an appropriate name automatically. Name is primarily intended for creation idempotence and configuration definition. Cannot be updated. More info: http://kubernetes.io/docs/user-guide/identifiers#names","priority":0},{"name":"Ready","type":"string","format":"","description":"The aggregate readiness state of this pod for accepting traffic.","priority":0},{"name":"Status","type":"string","format":"","description":"The aggregate status of the containers in this pod.","priority":0},{"name":"Restarts","type":"string","format":"","description":"The number of times the containers in this pod have been restarted and when the last container in this pod has restarted.","priority":0},{"name":"Age","type":"string","format":"","description":"CreationTimestamp is a timestamp representing the server time when this object was created. It is not guaranteed to be set in happens-before order across separate operations. Clients may not set this value. It is represented in RFC3339 form and is in UTC.\n\nPopulated by the system. Read-only. Null for lists. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata","priority":0},{"name":"IP","type":"string","format":"","description":"IP address allocated to the pod. Routable at least within the cluster. Empty if not yet allocated.","priority":1},{"name":"Node","type":"string","format":"","description":"NodeName is a request to schedule this pod onto a specific node. If it is non-empty, the scheduler simply schedules this pod onto that node, assuming that it fits resource requirements.","priority":1},{"name":"Nominated Node","type":"string","format":"","description":"nominatedNodeName is set only when this pod preempts other pods on the node, but it cannot be scheduled right away as preemption victims receive their graceful termination periods. This field does not guarantee that the pod will be scheduled on this node. Scheduler may decide to place the pod elsewhere if other nodes become available sooner. Scheduler may also decide to give the resources on this node to a higher priority pod that is created after preemption. As a result, this field may be different than PodSpec.nodeName when the pod is scheduled.","priority":1},{"name":"Readiness Gates","type":"string","format":"","description":"If specified, all readiness gates will be evaluated for pod readiness. A pod is ready when all its containers are ready AND all conditions specified in the readiness gates have status equal to \"True\" More info: https://git.k8s.io/enhancements/keps/sig-network/580-pod-readiness-gates","priority":1}],"rows":[{"cells":["ngx","1/1","Running","0","106m","10.244.0.3","minikube","\u003cnone\u003e","\u003cnone\u003e"],"object":{"kind":"PartialObjectMetadata","apiVersion":"meta.k8s.io/v1","metadata":{"name":"ngx","namespace":"default","uid":"24a0addd-b436-4c25-aa5b-d8456c90f7b8","resourceVersion":"2715","creationTimestamp":"2023-03-27T01:33:05Z","labels":{"run":"ngx"},"managedFields":[{"manager":"kubectl-run","operation":"Update","apiVersion":"v1","time":"2023-03-27T01:33:05Z","fieldsType":"FieldsV1","fieldsV1":{"f:metadata":{"f:labels":{".":{},"f:run":{}}},"f:spec":{"f:containers":{"k:{\"name\":\"ngx\"}":{".":{},"f:image":{},"f:imagePullPolicy":{},"f:name":{},"f:resources":{},"f:terminationMessagePath":{},"f:terminationMessagePolicy":{}}},"f:dnsPolicy":{},"f:enableServiceLinks":{},"f:restartPolicy":{},"f:schedulerName":{},"f:securityContext":{},"f:terminationGracePeriodSeconds":{}}}},{"manager":"kubelet","operation":"Update","apiVersion":"v1","time":"2023-03-27T01:33:09Z","fieldsType":"FieldsV1","fieldsV1":{"f:status":{"f:conditions":{"k:{\"type\":\"ContainersReady\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}},"k:{\"type\":\"Initialized\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}},"k:{\"type\":\"Ready\"}":{".":{},"f:lastProbeTime":{},"f:lastTransitionTime":{},"f:status":{},"f:type":{}}},"f:containerStatuses":{},"f:hostIP":{},"f:phase":{},"f:podIP":{},"f:podIPs":{".":{},"k:{\"ip\":\"10.244.0.3\"}":{".":{},"f:ip":{}}},"f:startTime":{}}},"subresource":"status"}]}}}]}
NAME   READY   STATUS    RESTARTS   AGE
ngx    1/1     Running   0          106m

```

从截图里可以看到, kubectl 客户端等价于调用了 curl, 向 8443 端口发送了 HTTP GET 请求, URL 是 /api/v1/namespaces/default/pods. 

目前的 Kubernetes 1.23 版本有 50 多种 API 对象, 全面地描述了集群的节点, 应用, 配置, 服务, 账号等等信息, apiserver 会把它们都存储在数据库 etcd 里, 然后 kubelet, scheduler, controller-manager 等组件通过 apiserver 来操作它们, 就在 API 对象这个抽象层次实现了对整个集群的管理. 

#### 使用 YAML 描述并创建 Pod 对象

如何以 YAML 语言, 使用 "声明式"在 Kubernetes 里描述并创建 API 对象. 

```shell
kubectl run ngx --image=nginx:alpine
```



```yaml
# header
apiVersion: v1
kind: Pod
metadata:
  # 给 Pod 起了个名字叫 ngx-pod
  name: ngx-pod
  # 给 Pod "贴"上了一些便于查找的标签, 分别是 env 和 owner. 
  labels:
    env: demo
    owner: chrono

# body
spec:
  containers:
  # 使用的镜像
  - image: nginx:alpine
    # 镜像的名称
    name: ngx
    # 容器对外暴露的端口
    ports:
    - containerPort: 80
```

因为 API 对象采用标准的 HTTP 协议, 为了方便理解, 我们可以借鉴一下 HTTP 的报文格式, 把 API 对象的描述分成 "header"和 "body"两部分. 

 "header"里的 apiVersion, kind, metadata 这三个字段是任何对象都必须有的, 而 "body"部分则会与对象特定相关, 每种对象会有不同的规格定义, 在 YAML 里就表现为 spec 字段（即 specification), 表示我们对对象的 "期望状态"（desired status). 



-  "header"包含的是 API 对象的基本信息, 有三个字段: apiVersion, kind, metadata. 

  - apiVersion 表示操作这种资源的 API 版本号, 由于 Kubernetes 的迭代速度很快, 不同的版本创建的对象会有差异, 为了区分这些版本就需要使用 apiVersion 这个字段, 比如 v1, v1alpha1, v1beta1 等等. 

  - kind 表示资源对象的类型, 这个应该很好理解, 比如 Pod, Node, Job, Service 等等. 

  - metadata 这个字段顾名思义, 表示的是资源的一些 "元信息", 也就是用来标记对象, 方便 Kubernetes 管理的一些信息. 



```shell
# 创建 api 对象
kubectl apply -f ngx-pod.yml
# 删除 api 对象
kubectl delete -f ngx-pod.yml
# 对象字段的详细说明
kubectl explain pod
kubectl explain pod.metadata
kubectl explain pod.spec
kubectl explain pod.spec.containers
```

#### 生成 kubectl run 对应的 yaml 文件

~/.bashrc

```
# kubectl
alias kubectl="minikube kubectl --"
source <(kubectl completion bash)
alias k=kubectl
complete -o default -F __start_kubectl k

export out="--dry-run=client -o yaml"

```



```shell
kubectl run ngx --image=nginx:alpine --dry-run=client -o yaml

export out="--dry-run=client -o yaml"
kubectl run ngx --image=nginx:alpine $out

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ngx
  name: ngx
spec:
  containers:
  - image: nginx:alpine
    name: ngx
    resources: {}
  dnsPolicy: ClusterFirst
  # 重启策略, 对于确实不需要重启的 pod, 可以设置 restartPolicy: Never
  restartPolicy: Always
status: {}

```



kubectl run ngx --image=busybox:latest $out

```yaml
# busy-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: busy-pod
  labels:
    owner: chrono
    env: demo
    region: north
    tier: back
 
spec:
  containers:
  - image: busybox:latest
    name: busy
    # 指定镜像的拉取策略, 可以是 Always/Never/IfNotPresent, 一般默认是 IfNotPresent
    imagePullPolicy: IfNotPresent
    # 定义 Pod 的环境变量, 和 Dockerfile 里的 ENV 指令有点类似, 但它是运行时指定的, 更加灵活可配置. 
    env:
      - name: os
        value: "ubuntu"
      - name: debug
        value: "on"
    # 定义容器启动时要执行的命令, 相当于 Dockerfile 里的 ENTRYPOINT 指令. 
    command:
      - /bin/echo
    # command 运行时的参数, 相当于 Dockerfile 里的 CMD 指令
    args:
      - "$(os), $(debug)"
```



```shell
# 启动 pod
kubectl apply -f busy-pod.yml
# 删除 pod
kubectl delete -f busy-pod.yml
# 使用名字删除 pod
kubectl delete pod busy-pod
# 查看 pod 运行日志
kubectl logs busy-pod

# 查看 pod 列表和运行状态
kubectl get pod

# 检查容器的详细状态
kubectl describe pod busy-pod

ubuntu@VM-0-23-ubuntu:~$ kubectl describe pod busy-pod
Name:             busy-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Mon, 27 Mar 2023 17:51:06 +0800
Labels:           env=demo
                  owner=chrono
                  region=north
                  tier=back
Annotations:      <none>
Status:           Running
IP:               10.244.0.6
IPs:
  IP:  10.244.0.6
Containers:
  busy:
    Container ID:  docker://39782e4c5ce2b2b24d50b32f1b17f925491521f9c3bbbe033e6f97e05be37bd7
    Image:         busybox:latest
    Image ID:      docker-pullable://busybox@sha256:b5d6fe0712636ceb7430189de28819e195e8966372edfc2d9409d79402a0dc16
    Port:          <none>
    Host Port:     <none>
    Command:
      /bin/echo
    Args:
      $(os), $(debug)
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Mon, 27 Mar 2023 17:51:53 +0800
      Finished:     Mon, 27 Mar 2023 17:51:53 +0800
    Last State:     Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Mon, 27 Mar 2023 17:51:22 +0800
      Finished:     Mon, 27 Mar 2023 17:51:22 +0800
    Ready:          False
    Restart Count:  3
    Environment:
      os:     ubuntu
      debug:  on
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-2wc6f (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  kube-api-access-2wc6f:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  56s               default-scheduler  Successfully assigned default/busy-pod to minikube
  Normal   Pulling    55s               kubelet            Pulling image "busybox:latest"
  Normal   Pulled     53s               kubelet            Successfully pulled image "busybox:latest" in 2.020284029s (2.020291092s including waiting)
  Normal   Created    9s (x4 over 53s)  kubelet            Created container busy
  Normal   Started    9s (x4 over 53s)  kubelet            Started container busy
  Normal   Pulled     9s (x3 over 53s)  kubelet            Container image "busybox:latest" already present on machine
  Warning  BackOff    8s (x5 over 52s)  kubelet            Back-off restarting failed container busy in pod busy-pod_default(ea193e32-6fd3-49ac-931a-e9ab26276b09)

```





```shell
# 复制文件到 pod 中

echo 'aaa' > a.txt
kubectl cp a.txt ngx-pod:/tmp

# 进入 pod 执行命令, 要在 Pod 后面加上 --, 把 kubectl 的命令与 Shell 命令分隔开
kubectl exec -it ngx-pod -- sh

# 注意: kubectl cp 和 kubectl exec 操作的是 Pod 中的容器, 需要用 -c 参数指定容器名, 不过因为大多数 Pod 里只有一个容器, 所以就省略了
```



### Job

面向对象的设计有许多基本原则, 其中有两条我认为比较恰当地描述了 Kubernetes 对象设计思路, 一个是 "单一职责", 另一个是 "组合优于继承". 

 "单一职责"的意思是对象应该只专注于做好一件事情, 不要贪大求全, 保持足够小的粒度才更方便复用和管理. 

 "组合优于继承"的意思是应该尽量让对象在运行时产生联系, 保持松耦合, 而不要用硬编码的方式固定对象的关系. 

应用这两条原则, 我们再来看 Kubernetes 的资源对象就会很清晰了. 因为 Pod 已经是一个相对完善的对象, 专门负责管理容器, 那么我们就不应该再 "画蛇添足"地盲目为它扩充功能, 而是要保持它的独立性, 容器之外的功能就需要定义其他的对象, 把 Pod 作为它的一个成员 "组合"进去. 

ubernetes 里的两大类业务. 一类是像 Nginx 这样长时间运行的 "在线业务", 另一类是像 busybox 这样短时间运行的 "离线业务"

 "在线业务"类型的应用有很多, 比如 Nginx, Node.js, MySQL, Redis 等等, 一旦运行起来基本上不会停, 也就是永远在线. 

而 "离线业务"类型的应用也并不少见, 它们一般不直接服务于外部用户, 只对内部用户有意义, 比如日志分析, 数据建模, 视频转码等等, 虽然计算量很大, 但只会运行一段时间.  "离线业务"的特点是必定会退出, 不会无期限地运行下去, 所以它的调度策略也就与 "在线业务"存在很大的不同, 需要考虑运行超时, 状态检查, 失败重试, 获取计算结果等管理事项. 

 "离线业务"也可以分为两种. 一种是 "临时任务", 跑完就完事了, 下次有需求了说一声再重新安排; 另一种是 "定时任务", 可以按时按点周期运行, 不需要过多干预. 

对应到 Kubernetes 里, "临时任务"就是 API 对象 Job, "定时任务"就是 API 对象 CronJob, 使用这两个对象你就能够在 Kubernetes 里调度管理任意的离线业务了. 

#### 创建 job yaml 文件

```shell

export out="--dry-run=client -o yaml"              # 定义Shell变量
kubectl create job echo-job --image=busybox $out

apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: echo-job
spec:
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - image: busybox
        name: echo-job
        resources: {}
      restartPolicy: Never
status: {}

```

```yaml
# echo-job.yml

apiVersion: batch/v1
kind: Job
metadata:
  name: echo-job

spec:
  # template 字段定义了一个 "应用模板", 里面嵌入了一个 Pod, 这样 Job 就可以从这个模板来创建出 Pod
  template:
    # Pod 对象
    spec:
      # 因为 Job 业务的特殊性, 所以还定义了 restartPolicy, 确定 Pod 运行失败时的策略
      # OnFailure 是失败原地重启容器, 而 Never 则是不重启容器, 让 Job 去重新调度生成一个新的 Pod
      restartPolicy: OnFailure
      containers:
      - image: busybox
        name: echo-job
        imagePullPolicy: IfNotPresent
        command: ["/bin/echo"]
        args: ["hello", "world"]
```



因为 Pod 被 Job 管理, 它就不会反复重启报错了, 而是会显示为 Completed 表示任务完成, 而 Job 里也会列出运行成功的作业数量, 这里只有一个作业, 所以就是 1/1. 

```shell
kubectl apply -f job.yml
kubectl get job
kubectl get pod

# 查看 job
ubuntu@VM-0-23-ubuntu:~$ kubectl get job
NAME       COMPLETIONS   DURATION   AGE
echo-job   1/1           4s         7s

# 查看 pod
ubuntu@VM-0-23-ubuntu:~$ kubectl get pod
NAME             READY   STATUS             RESTARTS        AGE
busy-pod         0/1     CrashLoopBackOff   12 (114s ago)   38m
echo-job-5r9bh   0/1     Completed          0               23s
ngx              1/1     Running            0               8h

# 查看 pod 的日志
ubuntu@VM-0-23-ubuntu:~$ kubectl logs echo-job-5r9bh
hello world


```



再创建一个 Job 对象, 名字叫 "sleep-job", 它随机睡眠一段时间再退出, 模拟运行时间较长的作业（比如 MapReduce). Job 的参数设置成 15 秒超时, 最多重试 2 次, 总共需要运行完 4 个 Pod, 但同一时刻最多并发 2 个 Pod: 

```yaml
# sleep-job.yml

apiVersion: batch/v1
kind: Job
metadata:
  name: sleep-job

spec:
  # Pod 运行的超时时间. 
  activeDeadlineSeconds: 15
  # Pod 的失败重试次数
  backoffLimit: 2
  # Job 完成需要运行多少个 Pod, 默认是 1 个
  completions: 4
  # 它与 completions 相关, 表示允许并发运行的 Pod 数量, 避免过多占用资源. 
  parallelism: 2

  template:
    spec:
      restartPolicy: OnFailure
      containers:
      - image: busybox
        name: echo-job
        imagePullPolicy: IfNotPresent
        command:
          - sh
          - -c
          - sleep $(($RANDOM % 10 + 1)) && echo done
```



```shell
ubuntu@VM-0-23-ubuntu:~$ kubectl apply -f sleep-job.yml
job.batch/sleep-job created

# 使用 kubectl apply 创建 Job 之后, 我们可以用 kubectl get pod -w 来实时观察 Pod 的状态, 看到 Pod 不断被排队, 创建, 运行的过程: 
ubuntu@VM-0-23-ubuntu:~$ kubectl get pod -w
NAME              READY   STATUS             RESTARTS         AGE
busy-pod          0/1     CrashLoopBackOff   13 (2m21s ago)   44m
echo-job-5r9bh    0/1     Completed          0                5m57s
ngx               1/1     Running            0                9h
sleep-job-62ffn   1/1     Running            0                5s
sleep-job-r6zq6   1/1     Running            0                5s
sleep-job-62ffn   0/1     Completed          0                6s
sleep-job-r6zq6   0/1     Completed          0                7s
sleep-job-62ffn   0/1     Completed          0                8s
sleep-job-r6zq6   0/1     Completed          0                9s
sleep-job-hprwb   0/1     Pending            0                0s
sleep-job-hprwb   0/1     Pending            0                0s
sleep-job-62ffn   0/1     Completed          0                9s
sleep-job-hprwb   0/1     ContainerCreating   0                0s
sleep-job-85d4v   0/1     Pending             0                0s
sleep-job-85d4v   0/1     Pending             0                0s
sleep-job-85d4v   0/1     ContainerCreating   0                0s
sleep-job-r6zq6   0/1     Completed           0                9s
sleep-job-85d4v   1/1     Running             0                1s
sleep-job-hprwb   1/1     Running             0                1s
sleep-job-hprwb   0/1     Completed           0                5s
sleep-job-hprwb   0/1     Terminating         0                6s
sleep-job-85d4v   1/1     Terminating         0                6s
sleep-job-hprwb   0/1     Terminating         0                6s
sleep-job-85d4v   1/1     Terminating         0                6s
sleep-job-85d4v   0/1     Terminating         0                6s
sleep-job-85d4v   0/1     Terminating         0                6s
sleep-job-85d4v   0/1     Terminating         0                6s
sleep-job-hprwb   0/1     Terminating         0                7s
sleep-job-hprwb   0/1     Terminating         0                7s
sleep-job-hprwb   0/1     Terminating         0                7s

ubuntu@VM-0-23-ubuntu:~$ kubectl get pod -w
NAME              READY   STATUS             RESTARTS         AGE
busy-pod          0/1     CrashLoopBackOff   13 (2m51s ago)   44m
echo-job-5r9bh    0/1     Completed          0                6m27s
ngx               1/1     Running            0                9h
sleep-job-62ffn   0/1     Completed          0                35s
sleep-job-r6zq6   0/1     Completed          0                35s

```

### CronJob

```shell

export out="--dry-run=client -o yaml"              # 定义Shell变量
kubectl create cj echo-cj --image=busybox --schedule="" $out

apiVersion: batch/v1
kind: CronJob
metadata:
  creationTimestamp: null
  name: echo-cj

spec:
  jobTemplate:
    metadata:
      creationTimestamp: null
      name: echo-cj
    spec:
      template:
        metadata:
          creationTimestamp: null
        spec:
          containers:
          - image: busybox
            name: echo-cj
            resources: {}
          restartPolicy: OnFailure
  schedule: ""
status: {}

```



```yaml
# echo-cronjob.yml

apiVersion: batch/v1
kind: CronJobvmrun
metadata:
  name: echo-cj

# 第一个 spec 是 CronJob 自己的对象规格声明
spec:
  # 定义任务周期运行的规则
  schedule: '*/1 * * * *'
  jobTemplate:
    # 第二个 spec 从属于 "jobTemplate", 它定义了一个 Job 对象. 
    spec:
      template:
        # 第二个 spec 从属于 "jobTemplate", 它定义了一个 Job 对象. 
        spec:
          restartPolicy: OnFailure
          containers:
          - image: busybox
            name: echo-cj
            imagePullPolicy: IfNotPresent
            command: ["/bin/echo"]
            args: ["hello", "world"]
```

```shell

kubectl apply -f cronjob.yml
kubectl get cj
kubectl get pod

```

注: 

1. Job 在运行结束后不会立即删除, 这是为了方便获取计算结果. 可以使用字段 ttlSecondsAfterFinish 设置保留的时限

2. CronJob 也不会无限地保留已经运行的 Job, 它默认只保留 3 个最近的执行结果, 可以使用 successfulJobsHistoryLimit 改变



### 配置信息

#### ConfigMap

```shell

export out="--dry-run=client -o yaml"        # 定义Shell变量
kubectl create cm info-cm $out

# ConfigMap 存储的是配置数据, 是静态的字符串, 并不是容器, 所以它们就不需要用 "spec"字段来说明运行时的 "规格"
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: info-cm

ConfigMap 要存储数据, 就需要用另一个含义更明确的字段 "data". 
要生成带有 "data"字段的 YAML 样板, 你需要在 kubectl create 后面多加一个参数 --from-literal , 表示从字面值生成一些数据: 


kubectl create cm info-cm --from-literal=k=v $out
apiVersion: v1
data:
  k: v
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: info-cm

```



```yaml
# info-cm.yml

apiVersion: v1
kind: ConfigMap
metadata:
  name: info-cm

data:
  count: '10'
  debug: 'on'
  path: '/etc/systemd'
  greeting: |
    say hello to kubernetes.
```



```shell
ubuntu@VM-0-23-ubuntu:~$ kubectl apply -f info-cm.yml
configmap/info created

ubuntu@VM-0-23-ubuntu:~$ kubectl get cm
NAME               DATA   AGE
info               4      97s
info-cm            4      15s
kube-root-ca.crt   1      10h

# ConfigMap 的 Key-Value 信息就已经存入了 etcd 数据库, 后续就可以被其他 API 对象使用. 
ubuntu@VM-0-23-ubuntu:~$ kubectl describe cm info-cm
Name:         info-cm
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
count:
----
10
debug:
----
on
greeting:
----
say hello to kubernetes.

path:
----
/etc/systemd

BinaryData
====

Events:  <none>

```



#### Secret



在 Kubernetes 里 Secret 对象又细分出很多类, 比如: 访问私有镜像仓库的认证信息身份识别的凭证信息HTTPS 通信的证书和私钥一般的机密信息（格式由用户自行解释)



```shell

kubectl create secret generic user-sc --from-literal=name=root $out

apiVersion: v1
kind: Secret
metadata:
  creationTimestamp: null
  name: user-sc

data:
  name: cm9vdA==


echo -n "123456" | base64
MTIzNDU2

echo cm9vdA== | base64 -d

```



```yaml
# user-sc.yml

apiVersion: v1
kind: Secret
metadata:
  name: user-sc

data:
  name: cm9vdA==  # root
  pwd: MTIzNDU2   # 123456
  db: bXlzcWw=    # mysql
```



```shell

kubectl apply -f user-sc.yml
kubectl get secret
kubectl describe secret user

ubuntu@VM-0-23-ubuntu:~$ kubectl get secret
NAME      TYPE     DATA   AGE
user-sc   Opaque   3      7s

ubuntu@VM-0-23-ubuntu:~$ kubectl describe secret user-sc
Name:         user-sc
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
name:  4 bytes
pwd:   6 bytes
db:    5 bytes

```



#### 以环境变量的方式使用 ConfigMap/Secret



```shell

kubectl explain pod.spec.containers.env.valueFrom

```



```yaml
# env-pod.yml

apiVersion: v1
kind: Pod
metadata:
  name: env-pod

spec:
  containers:
  # 以环境变量的方式使用配置信息
  - env:
      # 定义环境变量 COUNT, GREETING, 引用的是 ConfigMap 对象
      - name: COUNT
        # 指定 value 从何而来
        valueFrom:
          configMapKeyRef:
            # name"是 ConfigMap 对象的名字
            name: info-cm
            #  "key"字段分别是 "info"对象里的 count 和 greeting. 
            key: count
      - name: GREETING
        valueFrom:
          configMapKeyRef:
            name: info-cm
            key: greeting

      # 定义环境变量 USERNAME, PASSWORD, 引用的是 Secret 对象
      - name: USERNAME
        valueFrom:
          secretKeyRef:
            # name"指定 Secret 对象的名字 user-sc
            name: user-sc
            key: name
      - name: PASSWORD
        valueFrom:
          secretKeyRef:
            name: user-sc
            key: pwd

    image: busybox
    name: busy
    imagePullPolicy: IfNotPresent
    command: ["/bin/sleep", "300"]
```

```shell
# 用 kubectl apply 创建 Pod, 再用 kubectl exec 进入 Pod, 验证环境变量是否生效
kubectl apply -f env-pod.yml
kubectl exec -it env-pod -- sh

echo $COUNT
echo $GREETING
echo $USERNAME $PASSWORD
```



#### 以 Volume 的方式使用 ConfigMap/Secret

可以为 Pod "挂载（mount)"多个 Volume, 里面存放供 Pod 访问的数据, 这种方式有点类似 docker run -v, 虽然用法复杂了一些, 但功能也相应强大一些. 

在 Pod 里挂载 Volume 很容易, 只需要在 "spec"里增加一个 "volumes"字段, 然后再定义卷的名字和引用的 ConfigMap/Secret 就可以了. 要注意的是 Volume 属于 Pod, 不属于容器, 所以它和字段 "containers"是同级的, 都属于 "spec". 



```yaml

spec:
  # 定义两个 Volume, 分别引用 ConfigMap 和 Secret
  volumes:
  - name: cm-vol
    configMap:
      name: info
  - name: sec-vol
    secret:
      secretName: user

  containers:
  # volumeMounts 把定义好的 Volume 挂载到容器里的某个路径下
  - volumeMounts:
    # mountPath 指定挂载路径
    - mountPath: /tmp/cm-items
      # name 指定挂载 Volume 的名字
      name: cm-vol
    - mountPath: /tmp/sec-items
      name: sec-vol
```



```yaml
# vol-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: vol-pod

spec:
  volumes:
  - name: cm-vol
    configMap:
      name: info
  - name: sec-vol
    secret:
      secretName: user

  containers:
  - volumeMounts:
    - mountPath: /tmp/cm-items
      name: cm-vol
    - mountPath: /tmp/sec-items
      name: sec-vol

    image: busybox
    name: busy
    imagePullPolicy: IfNotPresent
    command: ["/bin/sleep", "300"]

```

```shell

kubectl apply -f vol-pod.yml
kubectl get pod

kubectl exec -it vol-pod -- sh

```

你会看到, ConfigMap 和 Secret 都变成了目录的形式, 而它们里面的 Key-Value 变成了一个个的文件, 而文件名就是 Key. 

因为这种形式上的差异, 以 Volume 的方式来使用 ConfigMap/Secret, 就和环境变量不太一样. 环境变量用法简单, 更适合存放简短的字符串, 而 Volume 更适合存放大数据量的配置文件, 在 Pod 里加载成文件后让应用直接读取使用. 

如果已经存在了一些配置文件, 可以使用参数 --from-file 从文件自动创建出 ConfigMap 和 Secret

Linux 里对环境变量的命名有限制, 不能使用 `-`  `.` 等特殊字符



### 总结

Kubernetes 的 Master/Node 架构是它具有自动化运维能力的关键

Kubernetes 把集群里的计算资源定义为节点（Node), 其中又划分成控制面和数据面两类. 

控制面是 Master 节点, 负责管理集群和运维监控应用, 里面的核心组件是 apiserver, etcd, scheduler, controller-manager. 

数据面是 Worker 节点, 受 Master 节点的管控, 里面的核心组件是 kubelet, kube-proxy, container-runtime. 

为了更好地管理集群和业务应用, Kubernetes 从现实世界中抽象出了许多概念, 称为 "API 对象", 描述这些对象就需要使用 YAML 语言. 

Kubernetes 里有很多的 API 对象, 其中最核心的对象是 "Pod", 它捆绑了一组存在密切协作关系的容器, 容器之间共享网络和存储, 在集群里必须一起调度一起运行. 通过 Pod 这个概念, Kubernetes 就简化了对容器的管理工作, 其他的所有任务都是通过对 Pod 这个最小单位的再包装来实现的（12 讲). 

除了核心的 Pod 对象, 基于 "单一职责"和 "对象组合"这两个基本原则, 我们又学习了 4 个比较简单的 API 对象, 分别是 Job/CronJob 和 ConfigMap/Secret. 

Job/CronJob 对应的是离线作业, 它们逐层包装了 Pod, 添加了作业控制和定时规则（13 讲). 

ConfigMap/Secret 对应的是配置信息, 需要以环境变量或者存储卷的形式注入进 Pod, 然后进程才能在运行时使用（14 讲). 

#### WordPress 网站搭建步骤

第一步当然是要编排 MariaDB 对象

```yaml
# maria-cm.yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: maria-cm

data:
  DATABASE: 'db'
  USER: 'wp'
  PASSWORD: '123'
  ROOT_PASSWORD: '123'
  
```

定义 Pod 对象 maria-pod, 把配置信息注入 Pod, 让 MariaDB 运行时从环境变量读取这些信息: 

```yaml
# mariadb-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: maria-pod
  labels:
    app: wordpress
    role: database

spec:
  containers:
  - image: mariadb:10
    name: maria
    imagePullPolicy: IfNotPresent
    ports:
    - containerPort: 3306

    # envFrom 可以一次性地把 ConfigMap 里的字段全导入进 Pod, 并且能够指定变量名的前缀
    envFrom:
    - prefix: 'MARIADB_'
      configMapRef:
        name: maria-cm
```



```shell

kubectl apply -f mariadb-pod.yml

# 想要获取 IP 地址需要加上参数 -o wide
kubectl get pod -o wide

# 现在数据库就成功地在 Kubernetes 集群里跑起来了, IP 地址是 "172.17.0.2", 注意这个地址和 Docker 的不同, 是 Kubernetes 里的私有网段. 
```

第二步, 编排 WordPress 对象, 还是先用 ConfigMap 定义它的环境变量: 

```yaml
# wp-cm.yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: wp-cm

data:
  #  "HOST"字段, 它必须是 MariaDB Pod 的 IP 地址, 如果不写正确 WordPress 会无法正常连接数据库. 
  HOST: '172.17.0.2'
  USER: 'wp'
  PASSWORD: '123'
  NAME: 'db'

```



```yaml
# wp-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: wp-pod
  labels:
    app: wordpress
    role: website

spec:
  containers:
  - image: wordpress:5
    name: wp-pod
    imagePullPolicy: IfNotPresent
    ports:
    - containerPort: 80

    envFrom:
    - prefix: 'WORDPRESS_DB_'
      configMapRef:
        name: wp-cm
```



```shell

kubectl apply -f wp-pod.yml
kubectl get pod -o wide

```

第三步是为 WordPress Pod 映射端口号, 让它在集群外可见. 

因为 Pod 都是运行在 Kubernetes 内部的私有网段里的, 外界无法直接访问, 想要对外暴露服务, 需要使用一个专门的 kubectl port-forward 命令, 它专门负责把本机的端口映射到在目标对象的端口号, 有点类似 Docker 的参数 -p, 经常用于 Kubernetes 的临时调试和测试. 

```shell

kubectl port-forward wp-pod 8080:80 &

```

如果想关闭端口转发, 需要敲命令 fg , 它会把后台的任务带回到前台, 然后就可以简单地用 "Ctrl + C"来停止转发了. 

第四步是创建反向代理的 Nginx, 让我们的网站对外提供服务. 



```
# nginx.conf

server {
  listen 80;
  default_type text/html;

  location / {
      proxy_http_version 1.1;
      proxy_set_header Host $host;
      proxy_pass http://127.0.0.1:8080;
  }
}

```



```shell
#!/bin/sh

# chrono @ 2022-04

# in browser:
# http://192.168.10.208:80

# kubectl port-forward wp-pod 8080:80 &
# kubectl port-forward proxy-pod 8080:80 &

cat << "EOF" > /tmp/proxy.conf
server {
  listen 80;
  default_type text/html;

  location / {
      proxy_http_version 1.1;
      proxy_set_header Host $host;

      proxy_pass http://127.0.0.1:8080;
  }
}
EOF

docker run -d --rm \
    --net=host \
    -v /tmp/proxy.conf:/etc/nginx/conf.d/default.conf \
    nginx:alpine


```

启动 nginx 容器

```shell

docker run -d --rm \
    --net=host \
    -v /tmp/proxy.conf:/etc/nginx/conf.d/default.conf \
    nginx:alpine
```

有了 Nginx 的反向代理之后, 就可以打开浏览器, 输入本机的 "127.0.0.1"或者是虚拟机的 IP 地址（我这里仍然是 "http://192.168.10.208"), 看到 WordPress 的界面: 



你也可以在 Kubernetes 里使用命令 kubectl logs 查看 WordPress, MariaDB 等 Pod 的运行日志, 来验证它们是否已经正确地响应了请求: 



使用 Dashboard 管理 Kubernetes

```shell

minikube dashboard

```



## kubeadmin

https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/



为了简化 Kubernetes 的部署工作, 让它能够更 "接地气", 社区里就出现了一个专门用来在集群中安装 Kubernetes 的工具, 名字就叫 "kubeadm", 意思就是 "Kubernetes 管理员". 

所谓的多节点集群, 要求服务器应该有两台或者更多, 为了简化我们只取最小值, 所以这个 Kubernetes 集群就只有两台主机, 一台是 Master 节点, 另一台是 Worker 节点. 当然, 在完全掌握了 kubeadm 的用法之后, 你可以在这个集群里添加更多的节点. 

而 Worker 节点没有管理工作, 只运行业务应用, 所以配置可以低一些, 为了节省资源我给它分配了 1 核 CPU 和 1GB 的内存, 可以说是低到不能再低了. 

基于模拟生产环境的考虑, 在 Kubernetes 集群之外还需要有一台起辅助作用的服务器. 

它的名字叫 Console, 意思是控制台, 我们要在上面安装命令行工具 kubectl, 所有对 Kubernetes 集群的管理命令都是从这台主机发出去的. 这也比较符合实际情况, 因为安全的原因, 集群里的主机部署好之后应该尽量少直接登录上去操作. 

要提醒你的是, Console 这台主机只是逻辑上的概念, 不一定要是独立, 你在实际安装部署的时候完全可以复用之前 minikube 的虚拟机, 或者直接使用 Master/Worker 节点作为控制台. 

#### 安装前的准备工作

包括改主机名, 改 Docker 配置, 改网络设置, 改交换分区这四步. 

第一, 由于 Kubernetes 使用主机名来区分集群里的节点, 所以每个节点的 hostname 必须不能重名. 你需要修改 "/etc/hostname"这个文件, 把它改成容易辨识的名字, 比如 Master 节点就叫 master, Worker 节点就叫 worker: 

```shell

sudo vi /etc/hostname

```

第二, 虽然 Kubernetes 目前支持多种容器运行时, 但 Docker 还是最方便最易用的一种, 所以我们仍然继续使用 Docker 作为 Kubernetes 的底层支持, 使用 apt 安装 Docker Engine（可参考第 1 讲). 

docker 20 开始, 默认驱动就是 systemd, 可以使用以下命令来查看

```shell

ubuntu@server01:~$ docker info | grep Cgroup
 Cgroup Driver: systemd
 Cgroup Version: 2

```



安装完成后需要你再对 Docker 的配置做一点修改, 在 "/etc/docker/daemon.json"里把 cgroup 的驱动程序改成 systemd , 然后重启 Docker 的守护进程, 具体的操作我列在了下面: 

```shell

cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

第三, 为了让 Kubernetes 能够检查, 转发网络流量, 你需要修改 iptables 的配置, 启用 "br_netfilter"模块: 

https://kubernetes.io/zh-cn/docs/setup/production-environment/container-runtimes/

转发 IPv4 并让 iptables 看到桥接流量

```shell
# imooc 课程
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward=1 # better than modify /etc/sysctl.conf
EOF

sudo sysctl --system

# 官方文档
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# 设置所需的 sysctl 参数, 参数在重新启动后保持不变
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# 应用 sysctl 参数而不重新启动
sudo sysctl --system

# 通过运行以下指令确认 br_netfilter 和 overlay 模块被加载: 
lsmod | grep br_netfilter
lsmod | grep overlay

# 通过运行以下指令确认 net.bridge.bridge-nf-call-iptables, net.bridge.bridge-nf-call-ip6tables 和 net.ipv4.ip_forward 系统变量在你的 sysctl 配置中被设置为 1: 
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
```

第四, 你需要修改 "/etc/fstab", 关闭 Linux 的 swap 分区, 提升 Kubernetes 的性能: 

关闭 Linux 的 swap 分区可能会提升 Kubernetes 的性能, 但这取决于您的应用程序的工作负载和可用的系统资源. 

在某些情况下, 当系统内存不足时, Linux会使用swap分区来将未使用的内存转移到硬盘上, 以释放更多内存用于活动进程. 但是, 将未使用的内存转移到硬盘上会降低系统性能. 因此, 如果您的应用程序的工作负载需要大量的内存, 而系统内存不足, 则关闭swap分区可能会提高性能. 

然而, 关闭swap分区也有一些潜在的风险. 例如, 如果您的系统内存不足, 且未关闭swap分区, 则可能会导致内存不足错误和应用程序崩溃. 此外, 如果您的应用程序需要大量的内存且系统内存不足, 则关闭swap分区可能会导致系统死机. 

因此, 在关闭swap分区之前, 您应该考虑您的应用程序的工作负载和可用的系统资源, 以确保这样做不会导致性能问题或系统不稳定. 同时, 您也应该谨慎地进行测试和监控, 以确保关闭swap分区不会对系统性能或稳定性产生负面影响. 

`注意: ` 必须要关闭 swap 分区, 否则在 kubeadm init 时会报错

```shell
root@server02:~# sudo kubeadm init \
    --pod-network-cidr=10.10.0.0/16 \
    --apiserver-advertise-address=192.168.10.210
[init] Using Kubernetes version: v1.26.3
[preflight] Running pre-flight checks
        [WARNING Swap]: swap is enabled; production deployments should disable swap unless testing the NodeSwap feature gate of the kubelet
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR CRI]: container runtime is not running: output: time="2023-03-28T10:16:09Z" level=fatal msg="validate service connection: CRI v1 runtime API is not implemented for endpoint \"unix:///var/run/containerd/containerd.sock\": rpc error: code = Unimplemented desc = unknown service runtime.v1.RuntimeService"
, error: exit status 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher

```



```shell
# Check if you have swap space enabled
sudo swapon --show
root@server02:~# sudo swapon --show
NAME      TYPE SIZE USED PRIO
/swap.img file 3.8G   0B   -2

# remove the swap entry from the /etc/fstab file. Open the file using a text editor with the following command:
# sudo nano /etc/fstab
# Find the line that references your swap partition or file and delete it. Save and exit the file.
sudo sed -ri '/\sswap\s/s/^#?/#/' /etc/fstab

# disable swap space
sudo swapoff -a
```



```shell
root@server01:~# cat /etc/fstab
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-7LAKs5lKcHQKXytimCD31JZgM1JLacG2uIvfqBT3bgULE4ceQnrTn6mq80UNcpGi / ext4 defaults 0 1
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/3c1930f3-4c87-4db4-8cac-5d0485d0aa31 /boot ext4 defaults 0 1
# /swap.img     none    swap    sw      0       0

```



#### 安装 kubeadm

https://kubernetes.io/zh-cn/docs/tasks/tools/

https://kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

查看 6443 端口是否可用

netstat -tlnp | grep 6443

kubeadm 可以直接从 Google 自己的软件仓库下载安装, 但国内的网络不稳定, 很难下载成功, 需要改用其他的软件源, 这里我选择了国内的某云厂商: 

https://developer.aliyun.com/mirror/kubernetes/

```shell
sudo apt-get update

sudo apt install -y apt-transport-https ca-certificates curl

curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -

cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF

或
# cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
# deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
# EOF

sudo apt update
```

更新了软件仓库, 我们就可以用 apt install 获取 kubeadm, kubelet 和 kubectl 这三个安装必备工具了. apt 默认会下载最新版本, 但我们也可以指定版本号, 比如使用和 minikube 相同的 "1.23.3": 

```shell
root@worker02:~# apt list -a kubeadm | grep 1.23

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

kubeadm/kubernetes-xenial 1.23.17-00 amd64
kubeadm/kubernetes-xenial 1.23.16-00 amd64
...

sudo apt install -y kubeadm=1.23.3-00 kubelet=1.23.3-00 kubectl=1.23.3-00
sudo apt install -y kubeadm=1.23.17-00 kubelet=1.23.17-00 kubectl=1.23.17-00
```



安装完成之后, 你可以用 kubeadm version, kubectl version 来验证版本是否正确: 

```shell

kubeadm version
root@server01:~# kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"26", GitVersion:"v1.26.3", GitCommit:"9e644106593f3f4aa98f8a84b23db5fa378900bd", GitTreeState:"clean", BuildDate:"2023-03-15T13:38:47Z", GoVersion:"go1.19.7", Compiler:"gc", Platform:"linux/amd64"}

kubectl version --client
root@server02:~# kubectl version --client
WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short.  Use --output=yaml|json to get the full version.
Client Version: version.Info{Major:"1", Minor:"26", GitVersion:"v1.26.3", GitCommit:"9e644106593f3f4aa98f8a84b23db5fa378900bd", GitTreeState:"clean", BuildDate:"2023-03-15T13:40:17Z", GoVersion:"go1.19.7", Compiler:"gc", Platform:"linux/amd64"}
Kustomize Version: v4.5.7

root@server02:~# kubectl version --short
Flag --short has been deprecated, and will be removed in the future. The --short output will become the default.
Client Version: v1.26.3
Kustomize Version: v4.5.7
The connection to the server localhost:8080 was refused - did you specify the right host or port?


root@server02:~# kubectl version --output=yaml
clientVersion:
  buildDate: "2023-03-15T13:40:17Z"
  compiler: gc
  gitCommit: 9e644106593f3f4aa98f8a84b23db5fa378900bd
  gitTreeState: clean
  gitVersion: v1.26.3
  goVersion: go1.19.7
  major: "1"
  minor: "26"
  platform: linux/amd64
kustomizeVersion: v4.5.7

The connection to the server localhost:8080 was refused - did you specify the right host or port?

root@server02:~# kubectl version --output=json
{
  "clientVersion": {
    "major": "1",
    "minor": "26",
    "gitVersion": "v1.26.3",
    "gitCommit": "9e644106593f3f4aa98f8a84b23db5fa378900bd",
    "gitTreeState": "clean",
    "buildDate": "2023-03-15T13:40:17Z",
    "goVersion": "go1.19.7",
    "compiler": "gc",
    "platform": "linux/amd64"
  },
  "kustomizeVersion": "v4.5.7"
}
The connection to the server localhost:8080 was refused - did you specify the right host or port?

```



另外按照 Kubernetes 官网的要求, 我们最好再使用命令 apt-mark hold , 锁定这三个软件的版本, 避免意外升级导致版本错误: 

```shell\

sudo apt-mark hold kubeadm kubelet kubectl

apt-mark showhold

sudo apt-mark unhold kubeadm kubelet kubectl
```

#### 下载 Kubernetes 组件镜像

前面我说过, kubeadm 把 apiserver, etcd, scheduler 等组件都打包成了镜像, 以容器的方式启动 Kubernetes, 但这些镜像不是放在 Docker Hub 上, 而是放在 Google 自己的镜像仓库网站 gcr.io, 而它在国内的访问很困难, 直接拉取镜像几乎是不可能的. 

使用命令 kubeadm config images list 可以查看安装 Kubernetes 所需的镜像列表, 参数 --kubernetes-version 可以指定版本号: 

```shell

root@server01:~# kubeadm config images list

registry.k8s.io/kube-apiserver:v1.26.3
registry.k8s.io/kube-controller-manager:v1.26.3
registry.k8s.io/kube-scheduler:v1.26.3
registry.k8s.io/kube-proxy:v1.26.3
registry.k8s.io/pause:3.9
registry.k8s.io/etcd:3.5.6-0
registry.k8s.io/coredns/coredns:v1.9.3

root@server01:~# kubeadm config images list --kubernetes-version=v1.23.17
registry.k8s.io/kube-apiserver:v1.23.17
registry.k8s.io/kube-controller-manager:v1.23.17
registry.k8s.io/kube-scheduler:v1.23.17
registry.k8s.io/kube-proxy:v1.23.17
registry.k8s.io/pause:3.6
registry.k8s.io/etcd:3.5.6-0
registry.k8s.io/coredns/coredns:v1.8.6

root@worker02:~# kubeadm config images pull --kubernetes-version=v1.23.17
[config/images] Pulled registry.k8s.io/kube-apiserver:v1.23.17
[config/images] Pulled registry.k8s.io/kube-controller-manager:v1.23.17
[config/images] Pulled registry.k8s.io/kube-scheduler:v1.23.17
[config/images] Pulled registry.k8s.io/kube-proxy:v1.23.17
[config/images] Pulled registry.k8s.io/pause:3.6
[config/images] Pulled registry.k8s.io/etcd:3.5.6-0
[config/images] Pulled registry.k8s.io/coredns/coredns:v1.8.6

```

虚拟机创建快照

```shell
vmrun.exe snapshot C:\vmware\k8s\server01\server01.vmx 20230329-pull-k8s
vmrun.exe snapshot C:\vmware\k8s\worker01\worker01.vmx 20230329-pull-k8s
vmrun.exe snapshot C:\vmware\k8s\worker02\worker02.vmx 20230329-pull-k8s
vmrun.exe snapshot C:\vmware\k8s\worker03\worker03.vmx 20230329-pull-k8s

vmrun.exe start C:\vmware\k8s\server01\server01.vmx
vmrun.exe start C:\vmware\k8s\worker01\worker01.vmx
vmrun.exe start C:\vmware\k8s\worker02\worker02.vmx
vmrun.exe start C:\vmware\k8s\worker03\worker03.vmx
```



#### 使用 kubeadm 创建集群

https://kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/

````shell
root@server01:~# kubeadm init -h
Run this command in order to set up the Kubernetes control plane

The "init" command executes the following phases:
```
preflight                    Run pre-flight checks
certs                        Certificate generation
  /ca                          Generate the self-signed Kubernetes CA to provision identities for other Kubernetes components
  /apiserver                   Generate the certificate for serving the Kubernetes API
  /apiserver-kubelet-client    Generate the certificate for the API server to connect to kubelet
  /front-proxy-ca              Generate the self-signed CA to provision identities for front proxy
  /front-proxy-client          Generate the certificate for the front proxy client
  /etcd-ca                     Generate the self-signed CA to provision identities for etcd
  /etcd-server                 Generate the certificate for serving etcd
  /etcd-peer                   Generate the certificate for etcd nodes to communicate with each other
  /etcd-healthcheck-client     Generate the certificate for liveness probes to healthcheck etcd
  /apiserver-etcd-client       Generate the certificate the apiserver uses to access etcd
  /sa                          Generate a private key for signing service account tokens along with its public key
kubeconfig                   Generate all kubeconfig files necessary to establish the control plane and the admin kubeconfig file
  /admin                       Generate a kubeconfig file for the admin to use and for kubeadm itself
  /kubelet                     Generate a kubeconfig file for the kubelet to use *only* for cluster bootstrapping purposes
  /controller-manager          Generate a kubeconfig file for the controller manager to use
  /scheduler                   Generate a kubeconfig file for the scheduler to use
kubelet-start                Write kubelet settings and (re)start the kubelet
control-plane                Generate all static Pod manifest files necessary to establish the control plane
  /apiserver                   Generates the kube-apiserver static Pod manifest
  /controller-manager          Generates the kube-controller-manager static Pod manifest
  /scheduler                   Generates the kube-scheduler static Pod manifest
etcd                         Generate static Pod manifest file for local etcd
  /local                       Generate the static Pod manifest file for a local, single-node local etcd instance
upload-config                Upload the kubeadm and kubelet configuration to a ConfigMap
  /kubeadm                     Upload the kubeadm ClusterConfiguration to a ConfigMap
  /kubelet                     Upload the kubelet component config to a ConfigMap
upload-certs                 Upload certificates to kubeadm-certs
mark-control-plane           Mark a node as a control-plane
bootstrap-token              Generates bootstrap tokens used to join a node to a cluster
kubelet-finalize             Updates settings relevant to the kubelet after TLS bootstrap
  /experimental-cert-rotation  Enable kubelet client certificate rotation
addon                        Install required addons for passing conformance tests
  /coredns                     Install the CoreDNS addon to a Kubernetes cluster
  /kube-proxy                  Install the kube-proxy addon to a Kubernetes cluster
show-join-command            Show the join command for control-plane and worker node
```

Usage:
  kubeadm init [flags]
  kubeadm init [command]

Available Commands:
  phase       Use this command to invoke single phase of the init workflow

Flags:
      --apiserver-advertise-address string   The IP address the API Server will advertise it's listening on. If not set the default network interface will be used.
      --apiserver-bind-port int32            Port for the API Server to bind to. (default 6443)
      --apiserver-cert-extra-sans strings    Optional extra Subject Alternative Names (SANs) to use for the API Server serving certificate. Can be both IP addresses and DNS names.
      --cert-dir string                      The path where to save and store the certificates. (default "/etc/kubernetes/pki")
      --certificate-key string               Key used to encrypt the control-plane certificates in the kubeadm-certs Secret.
      --config string                        Path to a kubeadm configuration file.
      --control-plane-endpoint string        Specify a stable IP address or DNS name for the control plane.
      --cri-socket string                    Path to the CRI socket to connect. If empty kubeadm will try to auto-detect this value; use this option only if you have more than one CRI installed or if you have non-standard CRI socket.
      --dry-run                              Don't apply any changes; just output what would be done.
      --feature-gates string                 A set of key=value pairs that describe feature gates for various features. Options are:
                                             PublicKeysECDSA=true|false (ALPHA - default=false)
                                             RootlessControlPlane=true|false (ALPHA - default=false)
  -h, --help                                 help for init
      --ignore-preflight-errors strings      A list of checks whose errors will be shown as warnings. Example: 'IsPrivilegedUser,Swap'. Value 'all' ignores errors from all checks.
      --image-repository string              Choose a container registry to pull control plane images from (default "registry.k8s.io")
      --kubernetes-version string            Choose a specific Kubernetes version for the control plane. (default "stable-1")
      --node-name string                     Specify the node name.
      --patches string                       Path to a directory that contains files named "target[suffix][+patchtype].extension". For example, "kube-apiserver0+merge.yaml" or just "etcd.json". "target" can be one of "kube-apiserver", "kube-controller-manager", "kube-scheduler", "etcd", "kubeletconfiguration". "patchtype" can be one of "strategic", "merge" or "json" and they match the patch formats supported by kubectl. The default "patchtype" is "strategic". "extension" must be either "json" or "yaml". "suffix" is an optional string that can be used to determine which patches are applied first alpha-numerically.
      --pod-network-cidr string              Specify range of IP addresses for the pod network. If set, the control plane will automatically allocate CIDRs for every node.
      --service-cidr string                  Use alternative range of IP address for service VIPs. (default "10.96.0.0/12")
      --service-dns-domain string            Use alternative domain for services, e.g. "myorg.internal". (default "cluster.local")
      --skip-certificate-key-print           Don't print the key used to encrypt the control-plane certificates.
      --skip-phases strings                  List of phases to be skipped
      --skip-token-print                     Skip printing of the default bootstrap token generated by 'kubeadm init'.
      --token string                         The token to use for establishing bidirectional trust between nodes and control-plane nodes. The format is [a-z0-9]{6}\.[a-z0-9]{16} - e.g. abcdef.0123456789abcdef
      --token-ttl duration                   The duration before the token is automatically deleted (e.g. 1s, 2m, 3h). If set to '0', the token will never expire (default 24h0m0s)
      --upload-certs                         Upload control-plane certificates to the kubeadm-certs Secret.

Global Flags:
      --add-dir-header           If true, adds the file directory to the header of the log messages
      --log-file string          If non-empty, use this log file (no effect when -logtostderr=true)
      --log-file-max-size uint   Defines the maximum size a log file can grow to (no effect when -logtostderr=true). Unit is megabytes. If the value is 0, the maximum file size is unlimited. (default 1800)
      --one-output               If true, only write logs to their native severity level (vs also writing to each lower severity level; no effect when -logtostderr=true)
      --rootfs string            [EXPERIMENTAL] The path to the 'real' host root filesystem.
      --skip-headers             If true, avoid header prefixes in the log messages
      --skip-log-headers         If true, avoid headers when opening log files (no effect when -logtostderr=true)
  -v, --v Level                  number for the log level verbosity

Use "kubeadm init [command] --help" for more information about a command.

````

[使用 Kubeadm 部署 | 凤凰架构 (icyfenix.cn)](http://icyfenix.cn/appendix/deployment-env-setup/setup-kubernetes/setup-kubeadm.html)

[A.2.3 集群初始化 · Kubernetes Documentation (renkeju.com)](https://kubernetes.renkeju.com/deploy/A.2.3.cluster-initialization.html)

kubeadm 的用法非常简单, 只需要一个命令 kubeadm init 就可以把组件在 Master 节点上运行起来, 不过它还有很多参数用来调整集群的配置, 你可以用 -h 查看. 这里我只说一下我们实验环境用到的 3 个参数: 

```
--pod-network-cidr, 设置集群里 Pod 的 IP 地址段. 参数是给Flannel网络做网段划分使用的

https://kubernetes.io/zh-cn/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network

--apiserver-advertise-address, 设置 apiserver 的 IP 地址, 对于多网卡服务器来说很重要（比如 VirtualBox 虚拟机就用了两块网卡), 可以指定 apiserver 在哪个网卡上对外提供服务. 

`--apiserver-advertise-address` 参数是APIServer的IP地址, 如果有多张物理网卡, 建议指定明确的IP地址来建立Kubernetes集群. 因为CA证书直接与地址相关, Kubernetes中诸多配置（配置文件, ConfigMap资源)也直接存储了这个地址, 一旦更换IP, 要想要不重置集群, 手工换起来异常麻烦. 

`–-apiserver-advertise-address` 指明用Master的哪个interface与Cluster 的其他节点通信.  如果Master有多个interface, 建议明确指定, 如果 不指定, kubeadm会自动选择有默认网关的interface. 

`--apiserver-advertise-address` API server 通告给其他组件的 IP 地址, 一般应该为 Master 节点的 IP 地址, 0.0.0.0 表示节点上所有可用的地址. 

`--kubernetes-version`参数指定 Kubernetes 的版本号. 要注意版本号与 kubelet 一致, 与前面预拉取是一样的, 避免额外的网络访问去查询版本号; 如果能够科学上网, 不需要加这个参数. 

（推荐)如果计划将单个控制平面 kubeadm 集群升级成高可用, 你应该指定 --control-plane-endpoint 为所有控制平面节点设置共享端点.  端点可以是负载均衡器的 DNS 名称或 IP 地址. 

root@server01:~# resolvectl status | grep Current
    Current Scopes: DNS
Current DNS Server: 192.168.116.2
Current Scopes: none

```



下面的这个安装命令里, 我指定了 Pod 的地址段是 "10.10.0.0/16", apiserver 的服务地址是 "192.168.10.210", Kubernetes 的版本号是 "1.23.3": 

```shell

sudo kubeadm init \
    --pod-network-cidr=10.10.0.0/16 \
    --apiserver-advertise-address=192.168.116.150 \
    --control-plane-endpoint=192.168.116.150 \
    --kubernetes-version=v1.23.17
    
```

kubeadm init 安装报错

```shell

root@server01:~# kubeadm init \
 --pod-network-cidr=10.10.0.0/16 \
 --apiserver-advertise-address=192.168.10.210
[init] Using Kubernetes version: v1.26.3
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR CRI]: container runtime is not running: output: time="2023-03-28T10:55:31Z" level=fatal msg="validate service connection: CRI v1 runtime API is not implemented for endpoint \"unix:///var/run/containerd/containerd.sock\": rpc error: code = Unimplemented desc = unknown service runtime.v1.RuntimeService"
, error: exit status 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher


```

解决方案

```shell
# rm -rf /etc/containerd/config.toml
mv /etc/containerd/config.toml /etc/containerd/config.toml.bak
systemctl restart containerd
```



```shell
master node
ubuntu@server01:~$ sudo kubeadm reset
[sudo] password for ubuntu:
[reset] Reading configuration from the cluster...
[reset] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
W0329 07:44:48.992975  469993 reset.go:106] [reset] Unable to fetch the kubeadm-config ConfigMap from cluster: failed to get config map: Get "https://192.168.116.150:6443/api/v1/namespaces/kube-system/configmaps/kubeadm-config?timeout=10s": dial tcp 192.168.116.150:6443: connect: connection refused
W0329 07:44:48.993296  469993 preflight.go:56] [reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
W0329 07:44:51.850180  469993 removeetcdmember.go:106] [reset] No kubeadm config, using etcd pod spec to get data directory
[reset] Deleted contents of the etcd data directory: /var/lib/etcd
[reset] Stopping the kubelet service
[reset] Unmounting mounted directories in "/var/lib/kubelet"
[reset] Deleting contents of directories: [/etc/kubernetes/manifests /var/lib/kubelet /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]

The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.


ubuntu@server01:~$ sudo kubeadm init \
 --pod-network-cidr=10.10.0.0/16 \
 --apiserver-advertise-address=192.168.10.210
[init] Using Kubernetes version: v1.26.3
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'

root@server01:~# kubeadm config images pull
[config/images] Pulled registry.k8s.io/kube-apiserver:v1.26.3
[config/images] Pulled registry.k8s.io/kube-controller-manager:v1.26.3
[config/images] Pulled registry.k8s.io/kube-scheduler:v1.26.3
[config/images] Pulled registry.k8s.io/kube-proxy:v1.26.3
[config/images] Pulled registry.k8s.io/pause:3.9
[config/images] Pulled registry.k8s.io/etcd:3.5.6-0
[config/images] Pulled registry.k8s.io/coredns/coredns:v1.9.3


root@server01:~# sudo kubeadm init --pod-network-cidr=10.10.0.0/16 --apiserver-advertise-address=192.168.116.150 --control-plane-endpoint=192.168.116.150 --kubernetes-version=v1.23.17
[init] Using Kubernetes version: v1.23.17
[preflight] Running pre-flight checks
        [WARNING SystemVerification]: this Docker version is not on the list of validated versions: 23.0.2. Latest validated version: 20.10
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local server01] and IPs [10.96.0.1 192.168.116.150]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [localhost server01] and IPs [192.168.116.150 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [localhost server01] and IPs [192.168.116.150 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 7.504834 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.23" in namespace kube-system with the configuration for the kubelets in the cluster
NOTE: The "kubelet-config-1.23" naming of the kubelet ConfigMap is deprecated. Once the UnversionedKubeletConfigMap feature gate graduates to Beta the default name will become just "kubelet-config". Kubeadm upgrade will handle this transition transparently.
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node server01 as control-plane by adding the labels: [node-role.kubernetes.io/master(deprecated) node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node server01 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: ktji70.rdy7e6mniugdzcur
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of control-plane nodes by copying certificate authorities
and service account keys on each node and then running the following as root:

  kubeadm join 192.168.116.150:6443 --token ktji70.rdy7e6mniugdzcur \
        --discovery-token-ca-cert-hash sha256:f6ff89f9089d3f0b90aff7029ddfe69a7d14ac5929751106de90edd7675a6d0e \
        --control-plane

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.116.150:6443 --token ktji70.rdy7e6mniugdzcur \
        --discovery-token-ca-cert-hash sha256:f6ff89f9089d3f0b90aff7029ddfe69a7d14ac5929751106de90edd7675a6d0e

```



```shell
ubuntu@server01:~$ kubectl version
Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.17", GitCommit:"953be8927218ec8067e1af2641e540238ffd7576", GitTreeState:"clean", BuildDate:"2023-02-22T13:34:27Z", GoVersion:"go1.19.6", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.17", GitCommit:"953be8927218ec8067e1af2641e540238ffd7576", GitTreeState:"clean", BuildDate:"2023-02-22T13:27:46Z", GoVersion:"go1.19.6", Compiler:"gc", Platform:"linux/amd64"}

ubuntu@server01:~$ kubectl get nodes
NAME       STATUS     ROLES                  AGE     VERSION
server01   NotReady   control-plane,master   3m23s   v1.23.17

ubuntu@server01:~$ kubectl delete node server02
node "server02" deleted

```



#### 配置网络

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/



[GitHub - flannel-io/flannel: flannel is a network fabric for containers, designed for Kubernetes](https://github.com/flannel-io/flannel/)

```shell
# kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

ubuntu@server01:~$ wget https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
--2023-03-29 12:38:23--  https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
Resolving github.com (github.com)... 192.30.255.112
Connecting to github.com (github.com)|192.30.255.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://github.com/flannel-io/flannel/releases/download/v0.21.4/kube-flannel.yml [following]
--2023-03-29 12:38:25--  https://github.com/flannel-io/flannel/releases/download/v0.21.4/kube-flannel.yml
Reusing existing connection to github.com:443.
HTTP request sent, awaiting response...
302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/21704134/39341d29-c730-4c07-89a6-a0c4e299cc6d?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20230329%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20230329T043825Z&X-Amz-Expires=300&X-Amz-Signature=efb1f446fbbfdbf40c440ea1db9d5e81c6c9d8f35ad140bd1d9cff3f4c01fd84&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=21704134&response-content-disposition=attachment%3B%20filename%3Dkube-flannel.yml&response-content-type=application%2Foctet-stream [following]
--2023-03-29 12:38:25--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/21704134/39341d29-c730-4c07-89a6-a0c4e299cc6d?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20230329%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20230329T043825Z&X-Amz-Expires=300&X-Amz-Signature=efb1f446fbbfdbf40c440ea1db9d5e81c6c9d8f35ad140bd1d9cff3f4c01fd84&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=21704134&response-content-disposition=attachment%3B%20filename%3Dkube-flannel.yml&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.108.133, 185.199.109.133, 185.199.110.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.108.133|:443... connected.
HTTP request sent, awaiting response...
200 OK
Length: 4459 (4.4K) [application/octet-stream]
Saving to: ‘kube-flannel.yml’

kube-flannel.yml                                    100%[=================================================================================================================>]   4.35K  --.-KB/s    in 0s

2023-03-29 12:38:26 (32.2 MB/s) - ‘kube-flannel.yml’ saved [4459/4459]

```



修改 net-conf.json 中的地址

```yaml

  net-conf.json: |
    {
      "Network": "10.10.0.0/16",
      "Backend": {
        "Type": "vxlan"
      }
    }

```



使用 kubectl apply 来安装 Flannel 网络了

```shell

kubectl apply -f kube-flannel.yml

```

稍等一小会, 等镜像拉取下来并运行之后, 你就可以执行 kubectl get node 来看节点状态: 

```shell
ubuntu@server01:~$ kubectl get node
NAME       STATUS   ROLES                  AGE     VERSION
server01   Ready    control-plane,master   9m57s   v1.23.17

```

#### 加入 worker 节点



```shell
kubeadm join 192.168.116.150:6443 --token ktji70.rdy7e6mniugdzcur \
        --discovery-token-ca-cert-hash sha256:f6ff89f9089d3f0b90aff7029ddfe69a7d14ac5929751106de90edd7675a6d0e

ubuntu@server01:~$ kubectl get nodes
NAME       STATUS   ROLES                  AGE     VERSION
server01   Ready    control-plane,master   65m     v1.23.17
worker01   Ready    <none>                 2m16s   v1.23.17
worker02   Ready    <none>                 2m15s   v1.23.17
worker03   Ready    <none>                 2m16s   v1.23.17

```



### Deployment

 "Deployment", 顾名思义, 它是专门用来部署应用程序的, 能够让应用永不宕机, 多用来发布无状态的应用, 是 Kubernetes 里最常用也是最有用的一个对象. 

API 对象 Job 和 CronJob, 它们代表了生产环境中的离线业务, 通过对 Pod 的包装, 向 Pod 添加控制字段, 实现了基于 Pod 运行临时任务和定时任务的功能. 

这个用来管理 Pod, 实现在线业务应用的新 API 对象, 就是 Deployment. 



```shell
$ kubectl api-resources | grep dep
deployments                       deploy       apps/v1                                true         Deployment

$ kubectl get deployment
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ngx-dep   1/1     1            1           7m24s

$ kubectl delete deployment ngx-dep

$ export out="--dry-run=client -o yaml"
$ kubectl create deploy ngx-dep --image=nginx:alpine $out
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ngx-dep
  name: ngx-dep

spec:
  # replicas  "副本数量"的意思, 指定要在 Kubernetes 集群里运行多少个 Pod 实例. 
  replicas: 1
  # selector 筛选"出要被 Deployment 管理的 Pod 对象
  selector:
    # matchLabels 定义了 Pod 对象应该携带的 label, 它必须和 "template"里 Pod 定义的 "labels"完全相同, 否则 Deployment 就会找不到要控制的 Pod 对象, apiserver 也会告诉你 YAML 格式校验错误无法创建. 
    matchLabels:
      app: ngx-dep
  strategy: {}
  template:
    # 以下定义了一个 pod
    metadata:
      creationTimestamp: null
      # labels 中的标签必须要和 matchLabels 中定义的完全相同
      labels:
        app: ngx-dep
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
        resources: {}
status: {}

```

replicas 字段

就是 "副本数量"的意思, 也就是说, 指定要在 Kubernetes 集群里运行多少个 Pod 实例. 

有了这个字段, 就相当于为 Kubernetes 明确了应用部署的 "期望状态", Deployment 对象就可以扮演运维监控人员的角色, 自动地在集群里调整 Pod 的数量. 

比如, Deployment 对象刚创建出来的时候, Pod 数量肯定是 0, 那么它就会根据 YAML 文件里的 Pod 模板, 逐个创建出要求数量的 Pod. 

接下来 Kubernetes 还会持续地监控 Pod 的运行状态, 万一有 Pod 发生意外消失了, 数量不满足 "期望状态", 它就会通过 apiserver, scheduler 等核心组件去选择新的节点, 创建出新的 Pod, 直至数量与 "期望状态"一致. 

selector字段

它的作用是 "筛选"出要被 Deployment 管理的 Pod 对象, 下属字段 "matchLabels"定义了 Pod 对象应该携带的 label, 它必须和 "template"里 Pod 定义的 "labels"完全相同, 否则 Deployment 就会找不到要控制的 Pod 对象, apiserver 也会告诉你 YAML 格式校验错误无法创建. 

这个 selector 字段的用法初看起来好像是有点多余, 为了保证 Deployment 成功创建, 我们必须在 YAML 里把 label 重复写两次: 一次是在 "selector.matchLabels", 另一次是在 "template.matadata". 像在这里, 你就要在这两个地方连续写 app: ngx-dep : 

你也许会产生疑问: 为什么要这么麻烦？为什么不能像 Job 对象一样, 直接用 "template"里定义好的 Pod 就行了呢？

这是因为在线业务和离线业务的应用场景差异很大. 离线业务中的 Pod 基本上是一次性的, 只与这个业务有关, 紧紧地绑定在 Job 对象里, 一般不会被其他对象所使用. 

而在线业务就要复杂得多了, 因为 Pod 永远在线, 除了要在 Deployment 里部署运行, 还可能会被其他的 API 对象引用来管理, 比如负责负载均衡的 Service 对象. 

所以 Deployment 和 Pod 实际上是一种松散的组合关系, Deployment 实际上并不 "持有"Pod 对象, 它只是帮助 Pod 对象能够有足够的副本数量运行, 仅此而已. 如果像 Job 那样, 把 Pod 在模板里 "写死", 那么其他的对象再想要去管理这些 Pod 就无能为力了. 

好明白了这一点, 那我们该用什么方式来描述 Deployment 和 Pod 的组合关系呢？

Kubernetes 采用的是这种 "贴标签"的方式, 通过在 API 对象的 "metadata"元信息里加各种标签（labels), 我们就可以使用类似关系数据库里查询语句的方式, 筛选出具有特定标识的那些对象. 通过标签这种设计, Kubernetes 就解除了 Deployment 和模板里 Pod 的强绑定, 把组合关系变成了 "弱引用". 



```yaml
# deploy.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: ngx-dep
  labels:
    app: ngx-dep

spec:
  # replicas  "副本数量"的意思, 指定要在 Kubernetes 集群里运行多少个 Pod 实例. 
  replicas: 2
  # selector 筛选"出要被 Deployment 管理的 Pod 对象
  selector:
    # matchLabels 定义了 Pod 对象应该携带的 label, 它必须和 "template"里 Pod 定义的 "labels"完全相同, 否则 Deployment 就会找不到要控制的 Pod 对象
    matchLabels:
      app: ngx-dep
  template:
    # 以下内容定义了一个 pod
    metadata:
      # labels 中的标签必须要和 matchLabels 中定义的完全相同
      labels:
        app: ngx-dep
    spec:
      containers:
      - image: nginx:alpine
        name: nginx

```

#### 操作 Deployment

```shell
# 创建 deployment 对象

kubectl apply -f deploy.yml

# 查看 deployment 的状态

kubectl get deploy
kubectl get deployment
kubectl get deployments

$ kubectl get deploy
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ngx-dep   2/2     2            2           72s
$ kubectl get deployment
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ngx-dep   2/2     2            2           76s
$ kubectl get deployments
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ngx-dep   2/2     2            2           81s

```

- READY 表示运行的 Pod 数量, 前面的数字是当前数量, 后面的数字是期望数量, 所以 "2/2"的意思就是要求有两个 Pod 运行, 现在已经启动了两个 Pod. 
- UP-TO-DATE 指的是当前已经更新到最新状态的 Pod 数量. 因为如果要部署的 Pod 数量很多或者 Pod 启动比较慢, Deployment 完全生效需要一个过程, UP-TO-DATE 就表示现在有多少个 Pod 已经完成了部署, 达成了模板里的 "期望状态". 
- AVAILABLE 要比 READY, UP-TO-DATE 更进一步, 不仅要求已经运行, 还必须是健康状态, 能够正常对外提供服务, 它才是我们最关心的 Deployment 指标. 
- AGE 表示 Deployment 从创建到现在所经过的时间, 也就是运行的时间. 


- 查看 pod

```shell

# 说明: 被 Deployment 管理的 Pod 自动带上了名字, 命名的规则是 Deployment 的名字加上两串随机数（其实是 Pod 模板的 Hash 值). 
kubectl get pods
# NAME                      READY   STATUS    RESTARTS   AGE
# ngx-dep-bfbb5f64b-bf6nz   1/1     Running   0          3m7s
# ngx-dep-bfbb5f64b-k99dj   1/1     Running   0          3m7s

# kubectl delete 删除一个 Pod, 模拟一下 Pod 发生故障的情景: 
kubectl delete pod ngx-dep-bfbb5f64b-bf6nz
# pod "ngx-dep-bfbb5f64b-bf6nz" deleted

# 被删除的 Pod 确实是消失了, 但 Kubernetes 在 Deployment 的管理之下, 很快又创建出了一个新的 Pod, 保证了应用实例的数量始终是我们在 YAML 里定义的数量. 
kubectl get pods -w
# NAME                      READY   STATUS    RESTARTS   AGE
# ngx-dep-bfbb5f64b-k99dj   1/1     Running   0          5m1s
# ngx-dep-bfbb5f64b-v6zrr   1/1     Running   0          4s

# kubectl scale 对 deployment 实现 "扩容"和 "缩容"
# 注意, kubectl scale 是命令式操作, 扩容和缩容只是临时的措施, 如果应用需要长时间保持一个确定的 Pod 数量, 最好还是编辑 Deployment 的 YAML 文件, 改动 "replicas", 再以声明式的 kubectl apply 修改对象的状态. 
kubectl scale --replicas=5 deploy ngx-dep

```

labels 字段的使用方法

之前我们通过 labels 为对象 "贴"了各种 "标签", 在使用 kubectl get 命令的时候, 加上参数 -l, 使用 ==, !=, in, notin 的表达式, 就能够很容易地用 "标签"筛选, 过滤出所要查找的对象（有点类似社交媒体的 #tag 功能), 效果和 Deployment 里的 selector 字段是一样的. 

```shell

# 找出 "app"标签是 nginx 的所有 Pod
kubectl get pod -l app=nginx

# 找出 "app"标签是 ngx, nginx, ngx-dep 的所有 Pod
kubectl get pod -l 'app in (ngx, nginx, ngx-dep)'


kubectl get pod -l app=nginx
# No resources found in default namespace.

kubectl get pod -l 'app in (ngx, nginx, ngx-dep)'
# NAME                      READY   STATUS    RESTARTS   AGE
# ngx-dep-bfbb5f64b-k99dj   1/1     Running   0          14m
# ngx-dep-bfbb5f64b-q9n87   1/1     Running   0          6m16s
# ngx-dep-bfbb5f64b-v6zrr   1/1     Running   0          9m16s

```

### Daemonset: 忠实可靠的看门狗

作用: 
DaemonSet, 它会在 Kubernetes 集群的每个节点上都运行一个 Pod, 就好像是 Linux 系统里的 "守护进程"（Daemon). 它在形式上和 Deployment 类似, 都是管理控制 Pod, 但管理调度策略却不同. DaemonSet 的目标是在集群的每个节点上运行且仅运行一个 Pod, 就好像是为节点配上一只 "看门狗", 忠实地 "守护"着节点, 这就是 DaemonSet 名字的由来. 

为什么引入 Daemonset
有一些业务比较特殊, 它们不是完全独立于系统运行的, 而是与主机存在 "绑定"关系, 必须要依附于节点才能产生价值, 比如说: 
网络应用（如 kube-proxy), 必须每个节点都运行一个 Pod, 否则节点就无法加入 Kubernetes 网络. 
监控应用（如 Prometheus), 必须每个节点都有一个 Pod 用来监控节点的状态, 实时上报信息. 
日志应用（如 Fluentd), 必须在每个节点上运行一个 Pod, 才能够搜集容器运行时产生的日志数据. 
安全应用, 同样的, 每个节点都要有一个 Pod 来执行安全审计, 入侵检查, 漏洞扫描等工作. 

daemonset 示例
https://kubernetes.io/zh/docs/concepts/workloads/controllers/daemonset/

```yaml
# https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/controllers/daemonset.yaml

apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging

spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # 这些容忍度设置是为了让该守护进程集在控制平面节点上运行
      # 如果你不希望自己的控制平面节点运行 Pod, 可以删除它们
      - key: node-role.kubernetes.io/control-plane
        operator: Exists
        effect: NoSchedule
      - key: node-role.kubernetes.io/master
        operator: Exists
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log

```

之前安装的网络插件 Flannel 就是一个 DaemonSet, 它位于命名空间 kube-system 中, 可以使用 `kubectl get ds -n kube-system` 命令看到

```shell

kubectl get ds -n kube-system
# NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
# kube-proxy   4         4         4       4            4           kubernetes.io/os=linux   46h

```

```shell

# 基于 YAML 文件创建 DaemonSet: 
kubectl apply -f https://k8s.io/examples/controllers/daemonset.yaml

```

```yaml
# daemonset-redis.yml

apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: redis-ds
  labels:
    app: redis-ds

spec:
  selector:
    matchLabels:
      name: redis-ds

  template:
    metadata:
      labels:
        name: redis-ds
    spec:
      containers:
      - image: redis:6-alpine
        name: redis
        ports:
        - containerPort: 6379

```

```shell
kubectl get pods
# NAME                      READY   STATUS    RESTARTS   AGE
# ngx-dep-bfbb5f64b-k99dj   1/1     Running   0          32m
# ngx-dep-bfbb5f64b-q9n87   1/1     Running   0          24m
# ngx-dep-bfbb5f64b-v6zrr   1/1     Running   0          27m
# redis-ds-fldq9            1/1     Running   0          6m38s
# redis-ds-fv5qd            1/1     Running   0          6m38s
# redis-ds-mpsq2            1/1     Running   0          6m38s

```

没有指定 DaemonSet 里要运行的 Pod 数量, 它自己就会去查找集群里的节点, 在节点里创建 Pod. Master 默认是不跑应用的, 所以 DaemonSet 就在每一个  "worker"节点创建了一个 Pod


按照 DaemonSet 的本意, 应该在每个节点上都运行一个 Pod 实例才对, 但 Master 节点却被排除在外了, 这就不符合我们当初的设想了. 

如果要在 Master 节点运行应用, 又与 "Master 默认是不跑应用的" 这个默认设定相违背了.
为了应对 Pod 在某些节点的 "调度"和 "驱逐"问题, 它定义了两个新的概念: 污点（taint)和容忍度（toleration). 

#### 污点（taint)和容忍度（toleration)

https://kubernetes.io/zh-cn/docs/concepts/scheduling-eviction/taint-and-toleration/

 "污点"是 Kubernetes 节点的一个属性, 它的作用也是给节点 "贴标签", 但为了不和已有的 labels 字段混淆, 就改成了 taint. 

Pod 的 "容忍度", 顾名思义, 就是 Pod 能否 "容忍"污点. 

集群里的节点各式各样, 有的节点 "纯洁无瑕", 没有 "污点"; 而有的节点因为某种原因粘上了 "泥巴", 也就有了 "污点". 

Pod 也脾气各异, 有的 "洁癖"很严重, 不能容忍 "污点", 只能挑选 "干净"的节点; 而有的 Pod 则比较 "大大咧咧", 要求不那么高, 可以适当地容忍一些小 "污点". 

这么看来, "污点"和 "容忍度"倒是有点像是一个 "相亲"的过程. Pod 就是一个挑剔的 "甲方", 而 "乙方"就是集群里的各个节点, Pod 会根据自己对 "污点"的 "容忍程度"来选择合适的目标, 比如要求 "不抽烟不喝酒", 但可以 "无车无房", 最终决定在哪个节点上 "落户". 

Kubernetes 在创建集群的时候会自动给节点 Node 加上一些 "污点", 方便 Pod 的调度和部署. 你可以用 kubectl describe node 来查看 Master 和 Worker 的状态: 


```shell

kubectl describe node server01
# Name:               server01
# Roles:              control-plane,master
# Labels:             beta.kubernetes.io/arch=amd64
#                     beta.kubernetes.io/os=linux
#                     kubernetes.io/arch=amd64
#                     kubernetes.io/hostname=server01
#                     kubernetes.io/os=linux
#                     node-role.kubernetes.io/control-plane=
#                     node-role.kubernetes.io/master=
#                     node.kubernetes.io/exclude-from-external-load-balancers=
# Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"96:c0:b0:ab:cc:6e"}
#                     flannel.alpha.coreos.com/backend-type: vxlan
#                     flannel.alpha.coreos.com/kube-subnet-manager: true
#                     flannel.alpha.coreos.com/public-ip: 192.168.116.150
#                     kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
#                     node.alpha.kubernetes.io/ttl: 0
#                     volumes.kubernetes.io/controller-managed-attach-detach: true
# CreationTimestamp:  Wed, 29 Mar 2023 12:33:42 +0800
# Taints:             node-role.kubernetes.io/master:NoSchedule
# Unschedulable:      false
# Lease:
#   HolderIdentity:  server01
#   AcquireTime:     <unset>
#   RenewTime:       Fri, 31 Mar 2023 10:29:57 +0800
# Conditions:
#   Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
#   ----                 ------  -----------------                 ------------------                ------                       -------
#   NetworkUnavailable   False   Wed, 29 Mar 2023 12:40:29 +0800   Wed, 29 Mar 2023 12:40:29 +0800   FlannelIsUp                  Flannel is running on this node
#   MemoryPressure       False   Fri, 31 Mar 2023 10:26:52 +0800   Wed, 29 Mar 2023 12:33:39 +0800   KubeletHasSufficientMemory   kubelet has sufficient memory available
#   DiskPressure         False   Fri, 31 Mar 2023 10:26:52 +0800   Wed, 29 Mar 2023 12:33:39 +0800   KubeletHasNoDiskPressure     kubelet has no disk pressure
#   PIDPressure          False   Fri, 31 Mar 2023 10:26:52 +0800   Wed, 29 Mar 2023 12:33:39 +0800   KubeletHasSufficientPID      kubelet has sufficient PID available
#   Ready                True    Fri, 31 Mar 2023 10:26:52 +0800   Wed, 29 Mar 2023 12:40:36 +0800   KubeletReady                 kubelet is posting ready status. AppArmor enabled
# Addresses:
#   InternalIP:  192.168.116.150
#   Hostname:    server01
# Capacity:
#   cpu:                4
#   ephemeral-storage:  29751268Ki
#   hugepages-1Gi:      0
#   hugepages-2Mi:      0
#   memory:             3983176Ki
#   pods:               110
# Allocatable:
#   cpu:                4
#   ephemeral-storage:  27418768544
#   hugepages-1Gi:      0
#   hugepages-2Mi:      0
#   memory:             3880776Ki
#   pods:               110
# System Info:
#   Machine ID:                 12e42a5e05244a898f146aee13477b76
#   System UUID:                c8964d56-dc94-b450-084b-399b4129fdbe
#   Boot ID:                    ef23b04a-a8a0-4626-8476-f2006d9dccfc
#   Kernel Version:             5.15.0-69-generic
#   OS Image:                   Ubuntu 22.04.2 LTS
#   Operating System:           linux
#   Architecture:               amd64
#   Container Runtime Version:  docker://23.0.2
#   Kubelet Version:            v1.23.17
#   Kube-Proxy Version:         v1.23.17
# PodCIDR:                      10.10.0.0/24
# PodCIDRs:                     10.10.0.0/24
# Non-terminated Pods:          (8 in total)
#   Namespace                   Name                                CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
#   ---------                   ----                                ------------  ----------  ---------------  -------------  ---
#   kube-flannel                kube-flannel-ds-fztgz               100m (2%)     0 (0%)      50Mi (1%)        0 (0%)         45h
#   kube-system                 coredns-bd6b6df9f-smdg6             100m (2%)     0 (0%)      70Mi (1%)        170Mi (4%)     45h
#   kube-system                 coredns-bd6b6df9f-xrc9j             100m (2%)     0 (0%)      70Mi (1%)        170Mi (4%)     45h
#   kube-system                 etcd-server01                       100m (2%)     0 (0%)      100Mi (2%)       0 (0%)         45h
#   kube-system                 kube-apiserver-server01             250m (6%)     0 (0%)      0 (0%)           0 (0%)         45h
#   kube-system                 kube-controller-manager-server01    200m (5%)     0 (0%)      0 (0%)           0 (0%)         45h
#   kube-system                 kube-proxy-h6ldm                    0 (0%)        0 (0%)      0 (0%)           0 (0%)         45h
#   kube-system                 kube-scheduler-server01             100m (2%)     0 (0%)      0 (0%)           0 (0%)         45h
# Allocated resources:
#   (Total limits may be over 100 percent, i.e., overcommitted.)
#   Resource           Requests    Limits
#   --------           --------    ------
#   cpu                950m (23%)  0 (0%)
#   memory             290Mi (7%)  340Mi (8%)
#   ephemeral-storage  0 (0%)      0 (0%)
#   hugepages-1Gi      0 (0%)      0 (0%)
#   hugepages-2Mi      0 (0%)      0 (0%)
# Events:              <none>


kubectl describe node worker01
# Name:               worker01
# Roles:              <none>
# Labels:             beta.kubernetes.io/arch=amd64
#                     beta.kubernetes.io/os=linux
#                     kubernetes.io/arch=amd64
#                     kubernetes.io/hostname=worker01
#                     kubernetes.io/os=linux
# Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"ae:5f:f2:ff:1e:c7"}
#                     flannel.alpha.coreos.com/backend-type: vxlan
#                     flannel.alpha.coreos.com/kube-subnet-manager: true
#                     flannel.alpha.coreos.com/public-ip: 192.168.116.153
#                     kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
#                     node.alpha.kubernetes.io/ttl: 0
#                     volumes.kubernetes.io/controller-managed-attach-detach: true
# CreationTimestamp:  Wed, 29 Mar 2023 13:36:36 +0800
# Taints:             <none>
# Unschedulable:      false
# Lease:
#   HolderIdentity:  worker01
#   AcquireTime:     <unset>
#   RenewTime:       Fri, 31 Mar 2023 10:30:10 +0800
# Conditions:
#   Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
#   ----                 ------  -----------------                 ------------------                ------                       -------
#   NetworkUnavailable   False   Wed, 29 Mar 2023 13:38:35 +0800   Wed, 29 Mar 2023 13:38:35 +0800   FlannelIsUp                  Flannel is running on this node
#   MemoryPressure       False   Fri, 31 Mar 2023 10:27:24 +0800   Wed, 29 Mar 2023 13:36:36 +0800   KubeletHasSufficientMemory   kubelet has sufficient memory available
#   DiskPressure         False   Fri, 31 Mar 2023 10:27:24 +0800   Wed, 29 Mar 2023 13:36:36 +0800   KubeletHasNoDiskPressure     kubelet has no disk pressure
#   PIDPressure          False   Fri, 31 Mar 2023 10:27:24 +0800   Wed, 29 Mar 2023 13:36:36 +0800   KubeletHasSufficientPID      kubelet has sufficient PID available
#   Ready                True    Fri, 31 Mar 2023 10:27:24 +0800   Wed, 29 Mar 2023 13:38:39 +0800   KubeletReady                 kubelet is posting ready status. AppArmor enabled
# Addresses:
#   InternalIP:  192.168.116.153
#   Hostname:    worker01
# Capacity:
#   cpu:                4
#   ephemeral-storage:  29751268Ki
#   hugepages-1Gi:      0
#   hugepages-2Mi:      0
#   memory:             3983176Ki
#   pods:               110
# Allocatable:
#   cpu:                4
#   ephemeral-storage:  27418768544
#   hugepages-1Gi:      0
#   hugepages-2Mi:      0
#   memory:             3880776Ki
#   pods:               110
# System Info:
#   Machine ID:                 12e42a5e05244a898f146aee13477b76
#   System UUID:                48eb4d56-c334-ad41-1217-287ba373062a
#   Boot ID:                    f2731a99-4294-4b3f-bbc4-8a22e8fbfd01
#   Kernel Version:             5.15.0-69-generic
#   OS Image:                   Ubuntu 22.04.2 LTS
#   Operating System:           linux
#   Architecture:               amd64
#   Container Runtime Version:  docker://23.0.2
#   Kubelet Version:            v1.23.17
#   Kube-Proxy Version:         v1.23.17
# PodCIDR:                      10.10.2.0/24
# PodCIDRs:                     10.10.2.0/24
# Non-terminated Pods:          (4 in total)
#   Namespace                   Name                       CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
#   ---------                   ----                       ------------  ----------  ---------------  -------------  ---
#   default                     ngx-dep-bfbb5f64b-v6zrr    0 (0%)        0 (0%)      0 (0%)           0 (0%)         36m
#   default                     redis-ds-fv5qd             0 (0%)        0 (0%)      0 (0%)           0 (0%)         15m
#   kube-flannel                kube-flannel-ds-twsd9      100m (2%)     0 (0%)      50Mi (1%)        0 (0%)         44h
#   kube-system                 kube-proxy-7thgx           0 (0%)        0 (0%)      0 (0%)           0 (0%)         44h
# Allocated resources:
#   (Total limits may be over 100 percent, i.e., overcommitted.)
#   Resource           Requests   Limits
#   --------           --------   ------
#   cpu                100m (2%)  0 (0%)
#   memory             50Mi (1%)  0 (0%)
#   ephemeral-storage  0 (0%)     0 (0%)
#   hugepages-1Gi      0 (0%)     0 (0%)
#   hugepages-2Mi      0 (0%)     0 (0%)
# Events:              <none>

```

```shell

# Master 节点默认有一个 taint, 它的效果是 NoSchedule, 
# 也就是说这个污点会拒绝 Pod 调度到本节点上运行, 而 Worker 节点的 taint 字段则是空的. 
# 通常来说 Pod 都不能容忍任何 "污点", 所以加上了 taint 属性的 Master 节点也就会无缘 Pod 了. 
kubectl describe node server01
# Taints:             node-role.kubernetes.io/master:NoSchedule

kubectl describe node worker01
# Taints:             <none>

```

#### 在 Master 节点运行 Pod

DaemonSet 在 Master 节点（或者任意其他节点)上运行了, 方法有两种. 

第一种方法
去掉 Master 节点上的 taint, 让 Master 变得和 Worker 一样 "纯洁无瑕", DaemonSet 自然就不需要再区分 Master/Worker. 

```shell
# kubectl taint, 然后指定节点名, 污点名和污点的效果, 去掉污点要额外加上一个 -
# 去掉 Master 节点的 "NoSchedule"效果
kubectl taint node server01 node-role.kubernetes.io/master:NoSchedule-

kubectl get pods
# NAME                      READY   STATUS              RESTARTS   AGE
# ngx-dep-bfbb5f64b-k99dj   1/1     Running             0          50m
# ngx-dep-bfbb5f64b-q9n87   1/1     Running             0          42m
# ngx-dep-bfbb5f64b-v6zrr   1/1     Running             0          45m
# redis-ds-bx7sv            0/1     ContainerCreating   0          10s
# redis-ds-fldq9            1/1     Running             0          25m
# redis-ds-fv5qd            1/1     Running             0          25m
# redis-ds-mpsq2            1/1     Running             0          25m


```

但是, 这种方法修改的是 Node 的状态, 影响面会比较大, 可能会导致很多 Pod 都跑到这个 master 节点上运行, 所以我们可以保留 Node 的 "污点", 为需要的 Pod 添加 "容忍度", 只让某些 Pod 运行在个别节点上, 实现 "精细化"调度. 

第二种方法, 为 Pod 添加字段 tolerations, 让它能够 "容忍"某些 "污点", 就可以在任意的节点上运行了. 

tolerations 是一个数组, 里面可以列出多个被 "容忍"的 "污点", 需要写清楚 "污点"的名字, 效果. 比较特别是要用 operator 字段指定如何匹配 "污点", 一般我们都使用 Exists, 也就是说存在这个名字和效果的 "污点". 

如果我们想让 DaemonSet 里的 Pod 能够在 Master 节点上运行, 就要写出这样的一个 tolerations, 容忍节点的 node-role.kubernetes.io/master:NoSchedule 这个污点: 

```yaml

tolerations:
- key: node-role.kubernetes.io/master
  effect: NoSchedule
  operator: Exists

```


```shell

# 先用 kubectl taint 命令把 Master 的 "污点"加上: 
kubectl taint node server01 node-role.kubernetes.io/master:NoSchedule

```

给 DaemonSet 加上  "容忍度"

```yaml
# daemonset-redis.yml

apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: redis-ds
  labels:
    app: redis-ds

spec:
  selector:
    matchLabels:
      name: redis-ds

  template:
    metadata:
      labels:
        name: redis-ds
    spec:
      containers:
      - image: redis:6-alpine
        name: redis
        ports:
        - containerPort: 6379

      tolerations:
      # this toleration is to have the daemonset runnable on master nodes
      # remove it if your masters can't run pods
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
        operator: Exists

```

注意, "容忍度"并不是 DaemonSet 独有的概念, 而是从属于 Pod, 所以理解了 "污点"和 "容忍度"之后, 你可以在 Job/CronJob, Deployment 里为它们管理的 Pod 也加上 tolerations, 从而能够更灵活地调度应用. 


#### 静态 Pod

DaemonSet 是在 Kubernetes 里运行节点专属 Pod 最常用的方式, 但它不是唯一的方式, Kubernetes 还支持另外一种叫 "静态 Pod"的应用部署手段. 

 "静态 Pod"非常特殊, 它不受 Kubernetes 系统的管控, 不与 apiserver, scheduler 发生关系, 所以是 "静态"的. 

但既然它是 Pod, 也必然会 "跑"在容器运行时上, 也会有 YAML 文件来描述它, 而唯一能够管理它的 Kubernetes 组件也就只有在每个节点上运行的 kubelet 了. 

 "静态 Pod"的 YAML 文件默认都存放在节点的 /etc/kubernetes/manifests 目录下, 它是 Kubernetes 的专用目录. 

你可以看到, Kubernetes 的 4 个核心组件 apiserver, etcd, scheduler, controller-manager 原来都以静态 Pod 的形式存在的, 这也是为什么它们能够先于 Kubernetes 集群启动的原因. 

```shell

pwd
# /etc/kubernetes/manifests

ll
# total 24
# drwxr-xr-x 2 root root 4096 Mar 29 12:33 ./
# drwxr-xr-x 4 root root 4096 Mar 29 12:33 ../
# -rw------- 1 root root 2284 Mar 29 12:33 etcd.yaml
# -rw------- 1 root root 3875 Mar 29 12:33 kube-apiserver.yaml
# -rw------- 1 root root 3370 Mar 29 12:33 kube-controller-manager.yaml
# -rw------- 1 root root 1441 Mar 29 12:33 kube-scheduler.yaml

```

如果你有一些 DaemonSet 无法满足的特殊的需求, 可以考虑使用静态 Pod, 编写一个 YAML 文件放到这个目录里, 节点的 kubelet 会定期检查目录里的文件, 发现变化就会调用容器运行时创建或者删除静态 Pod. 


### Service

https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/

在 Kubernetes 集群里 Pod 的生命周期是比较 "短暂"的, 虽然 Deployment 和 DaemonSet 可以维持 Pod 总体数量的稳定, 但在运行过程中, 难免会有 Pod 销毁又重建, 这就会导致 Pod 集合处于动态的变化之中. 

这种 "动态稳定"对于现在流行的微服务架构来说是非常致命的, 试想一下, 后台 Pod 的 IP 地址老是变来变去, 客户端该怎么访问呢？如果不处理好这个问题, Deployment 和 DaemonSet 把 Pod 管理得再完善也是没有价值的. 

其实, 这个问题也并不是什么难事, 业内早就有解决方案来针对这样 "不稳定"的后端服务, 那就是 "负载均衡", 典型的应用有 LVS, Nginx 等等. 它们在前端与后端之间加入了一个 "中间层", 屏蔽后端的变化, 为前端提供一个稳定的服务. 

但 LVS, Nginx 毕竟不是云原生技术, 所以 Kubernetes 就按照这个思路, 定义了新的 API 对象: Service. 

Service 的工作原理和 LVS, Nginx 差不多, Kubernetes 会给它分配一个静态 IP 地址, 然后它再去自动管理, 维护后面动态变化的 Pod 集合, 当客户端访问 Service, 它就根据某种策略, 把流量转发给后面的某个 Pod. 

Service 使用了 iptables 技术, 每个节点上的 kube-proxy 组件自动维护 iptables 规则, 客户不再关心 Pod 的具体地址, 只要访问 Service 的固定 IP 地址, Service 就会根据 iptables 规则转发请求给它管理的多个 Pod, 是典型的负载均衡架构. 

```shell

export out="--dry-run=client -o yaml"
kubectl expose deploy ngx-dep --port=80 --target-port=80 $out


apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: ngx-dep
  name: ngx-dep

spec:
  # ports 就很好理解了, 里面的三个字段分别表示外部端口, 内部端口和使用的协议, 在这里就是内外部都使用 80 端口, 协议是 TCP. 
  ports:
  # Pod 外部端口, 即宿主机端口
  - port: 80
    # Pod 内部端口
    # 与 Deployment yaml 中 spec.containers.ports 中定义的端口保持一致
    targetPort: 80
    # 使用的协议
    protocol: TCP
  # selector 和 Deployment/DaemonSet 里的作用是一样的, 用来过滤出要代理的那些 Pod. 因为我们指定要代理 Deployment, 所以 Kubernetes 就为我们自动填上了 ngx-dep 的标签, 会选择这个 Deployment 对象部署的所有 Pod. 
  # 对应 deployment yaml 中 spec.template.metadata.labels 中定义的 app
  selector:
    app: ngx-dep

status:
  loadBalancer: {}

```

创建 ConfigMap, 在其中定义一个 Nginx 的配置片段, 它会输出服务器的地址、主机名、请求的 URI 等基本信息

```yaml
# ngx-conf-cm.yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ngx-conf

data:
  default.conf: |
    server {
      listen 80;
      location / {
        default_type text/plain;
        return 200
          'srv : $server_addr:$server_port\nhost: $hostname\nuri : $request_method $host $request_uri\ndate: $time_iso8601\n';
      }
    }

```



```shell
kubectl apply -f ngx-conf-cm.yml
```

然后我们在 Deployment 的 "template.volumes"里定义存储卷, 再用 "volumeMounts"把配置文件加载进 Nginx 容器里: 


```yaml
# deploy-ngx.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: ngx-dep

spec:
  replicas: 2
  selector:
    matchLabels:
      app: ngx-dep

  template:
    metadata:
      labels:
        app: ngx-dep
    spec:
      volumes:
      - name: ngx-conf-vol
        configMap:
          name: ngx-conf

      containers:
      - image: nginx:alpine
        name: nginx
        ports:
        - containerPort: 80

        volumeMounts:
        - mountPath: /etc/nginx/conf.d
          name: ngx-conf-vol
```

部署 deployment

```shell

kubectl apply -f deploy-ngx.yml

```

创建 service 配置文件

```yaml
# service-ngx.yml
# chrono @ 2022-05

# kubectl expose deploy ngx-dep --port=80 $out
# kubectl apply -f svc.yml
# kubectl describe svc ngx-svc
# kubectl get pod -o wide
# kubectl exec -it ngx-dep-785b6bbbd7-7hmtv -- sh

apiVersion: v1
kind: Service
metadata:
  name: ngx-svc

spec:
  selector:
    app: ngx-dep

  ports:
  - port: 80
    protocol: TCP
    targetPort: 80

  #type: ClusterIP
  type: NodePort

```



```shell

# 创建 Service 对象
kubectl apply -f service-ngx.yml

# 查看 Service 对象
# Kubernetes 为 Service 对象自动分配了一个 IP 地址 "10.96.240.115", 
# 这个地址段是独立于 Pod 地址段的（比如第 17 讲里的 10.10.xx.xx). 
# 而且 Service 对象的 IP 地址还有一个特点, 它是一个 "虚地址", 不存在实体, 只能用来转发流量. 
kubectl get service
# NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
# kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   4d21h
# ngx-svc      ClusterIP   10.104.67.95    <none>        80/TCP    2d16h

# 查看 Service 代理了哪些后端的 Pod
# 可见 Service 对象管理了两个 endpoint, 分别是 "10.10.0.232:80"和 "10.10.1.86:80", 
# 初步判断与 Service、Deployment 的定义相符, 那么这两个 IP 地址是不是 Nginx Pod 的实际地址呢
# 使用命令 `kubectl get pod -o wide` 查看 pod 的详情
kubectl describe svc ngx-svc
# Name:                     ngx-svc
# Namespace:                default
# Labels:                   <none>
# Annotations:              <none>
# Selector:                 app=ngx-dep
# Type:                     NodePort
# IP Family Policy:         SingleStack
# IP Families:              IPv4
# IP:                       10.104.67.95
# IPs:                      10.104.67.95
# Port:                     <unset>  80/TCP
# TargetPort:               80/TCP
# NodePort:                 <unset>  32447/TCP
# Endpoints:                10.10.1.10:80,10.10.3.10:80
# Session Affinity:         None
# External Traffic Policy:  Cluster
# Events:                   <none>

# 把 Pod 的地址与 Service 的信息做个对比, 
# 我们就能够验证 Service 确实用一个静态 IP 地址代理了两个 Pod 的动态 IP 地址. 
kubectl get pod -o wide
# NAME                       READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
# ngx-dep-6796688696-rcdjl   1/1     Running   0          111s    10.10.1.10   worker03   <none>           <none>
# ngx-dep-6796688696-rvd42   1/1     Running   0          113s    10.10.3.10   worker02   <none>           <none>
# redis-ds-f67kg             1/1     Running   0          6h54m   10.10.2.9    worker01   <none>           <none>
# redis-ds-lzvxr             1/1     Running   0          6h54m   10.10.3.9    worker02   <none>           <none>
# redis-ds-xhr8n             1/1     Running   0          6h54m   10.10.1.9    worker03   <none>           <none>

# 测试 Service 的负载均衡效果
# 因为 Service、 Pod 的 IP 地址都是 Kubernetes 集群的内部网段, 
# 所以我们需要用 kubectl exec 进入到 Pod 内部（或者 ssh 登录集群节点), 
# 再用 curl 等工具来访问 Service: 
# 在 Pod 里, 用 curl 访问 Service 的 IP 地址, 就会看到它把数据转发给后端的 Pod, 
# 输出信息会显示具体是哪个 Pod 响应了请求, 就表明 Service 确实完成了对 Pod 的负载均衡任务. 
kubectl exec -it ngx-dep-6796688696-rcdjl -- sh

curl 10.104.67.95
# srv : 10.10.1.10:80
# host: ngx-dep-6796688696-rcdjl
# uri : GET 10.104.67.95 /
# date: 2023-03-31T10:08:29+00:00

curl 10.104.67.95
# srv : 10.10.1.10:80
# host: ngx-dep-6796688696-rcdjl
# uri : GET 10.104.67.95 /
# date: 2023-03-31T10:08:45+00:00

curl 10.104.67.95
# srv : 10.10.3.10:80
# host: ngx-dep-6796688696-rvd42
# uri : GET 10.104.67.95 /
# date: 2023-03-31T10:09:07+00:00

curl 10.104.67.95
# srv : 10.10.3.10:80
# host: ngx-dep-6796688696-rvd42
# uri : GET 10.104.67.95 /
# date: 2023-03-31T10:09:13+00:00

curl 10.104.67.95
# srv : 10.10.3.10:80
# host: ngx-dep-6796688696-rvd42
# uri : GET 10.104.67.95 /
# date: 2023-03-31T10:09:15+00:00

curl 10.104.67.95
# srv : 10.10.1.10:80
# host: ngx-dep-6796688696-rcdjl
# uri : GET 10.104.67.95 /
# date: 2023-03-31T10:09:17+00:00

exit

# 我们再试着删除一个 Pod, 看看 Service 是否会更新后端 Pod 的信息, 实现自动化的服务发现: 
# 由于 Pod 被 Deployment 对象管理, 删除后会自动重建, 
# 而 Service 又会通过 controller-manager 实时监控 Pod 的变化情况, 
# 所以就会立即更新它代理的 IP 地址. 通过截图你就可以看到有一个 IP 地址 "10.10.1.86"消失了, 
# 换成了新的 "10.10.1.87", 它就是新创建的 Pod. 
kubectl delete pod ngx-dep-6796688696-rcdjl
# pod "ngx-dep-6796688696-rcdjl" deleted

kubectl get pod -o wide
# NAME                       READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
# ngx-dep-6796688696-klnmr   1/1     Running   0          26s     10.10.2.10   worker01   <none>           <none>
# ngx-dep-6796688696-rvd42   1/1     Running   0          5m3s    10.10.3.10   worker02   <none>           <none>
# redis-ds-f67kg             1/1     Running   0          6h57m   10.10.2.9    worker01   <none>           <none>
# redis-ds-lzvxr             1/1     Running   0          6h57m   10.10.3.9    worker02   <none>           <none>
# redis-ds-xhr8n             1/1     Running   0          6h57m   10.10.1.9    worker03   <none>           <none>


```

你也可以再尝试一下使用 "ping"来测试 Service 的 IP 地址: 
会发现根本 ping 不通, 因为 Service 的 IP 地址是 "虚"的, 只用于转发流量, 所以 ping 无法得到回应数据包, 也就失败了. 

```shell

kubectl exec -it ngx-dep-6796688696-klnmr -- sh
curl 10.104.67.95
# srv : 10.10.3.10:80
# host: ngx-dep-6796688696-rvd42
# uri : GET 10.104.67.95 /
# date: 2023-03-31T10:45:51+00:00

ping 10.104.67.95
PING 10.104.67.95 (10.104.67.95): 56 data bytes
^C
--- 10.104.67.95 ping statistics ---
5 packets transmitted, 0 packets received, 100% packet loss

```

#### 以域名的方式使用 Service

Service 对象的 IP 地址是静态的, 保持稳定, 这在微服务里确实很重要, 不过数字形式的 IP 地址用起来还是不太方便. 这个时候 Kubernetes 的 DNS 插件就派上了用处, 它可以为 Service 创建易写易记的域名, 让 Service 更容易使用. 

- 名字空间（namespace)

namespace 在集群里实现对 API 对象的隔离和分组. 

kubectl get ns 来查看当前集群里都有哪些名字空间, 也就是说 API 对象有哪些分组: 

```shell

kubectl get ns
# NAME              STATUS   AGE
# default           Active   4d21h
# kube-flannel      Active   4d21h
# kube-node-lease   Active   4d21h
# kube-public       Active   4d21h
# kube-system       Active   4d21h

```

Kubernetes 有一个默认的名字空间, 叫 "default", 如果不显式指定, API 对象都会在这个 "default"名字空间里. 而其他的名字空间都有各自的用途, 比如 "kube-system"就包含了 apiserver、etcd 等核心组件的 Pod. 

因为 DNS 是一种层次结构, 为了避免太多的域名导致冲突, Kubernetes 就把名字空间作为域名的一部分, 减少了重名的可能性. 

Service 对象的域名完全形式是 "对象. 名字空间.svc.cluster.local", 但很多时候也可以省略后面的部分, 直接写 "对象. 名字空间"甚至 "对象名"就足够了, 默认会使用对象所在的名字空间（比如这里就是 default). 

试验一下 DNS 域名的用法, 还是先 kubectl exec 进入 Pod, 然后用 curl 访问 ngx-svc、ngx-svc.default 等域名: 


```shell

kubectl get pods
# NAME                       READY   STATUS    RESTARTS   AGE
# ngx-dep-6796688696-klnmr   1/1     Running   0          2d15h
# ngx-dep-6796688696-rvd42   1/1     Running   0          2d15h
# redis-ds-f67kg             1/1     Running   0          2d22h
# redis-ds-lzvxr             1/1     Running   0          2d22h
# redis-ds-xhr8n             1/1     Running   0          2d22h

kubectl exec -it ngx-dep-6796688696-klnmr -- sh

curl ngx-svc
# srv : 10.10.3.10:80
# host: ngx-dep-6796688696-rvd42
# uri : GET ngx-svc /
# date: 2023-04-03T02:02:18+00:00

curl ngx-svc.default
# srv : 10.10.3.10:80
# host: ngx-dep-6796688696-rvd42
# uri : GET ngx-svc.default /
# date: 2023-04-03T02:02:35+00:00

curl ngx-svc.default.svc.cluster.local
# srv : 10.10.2.10:80
# host: ngx-dep-6796688696-klnmr
# uri : GET ngx-svc.default.svc.cluster.local /
# date: 2023-04-03T02:02:51+00:00

exit

```

可以看到, 现在我们就不再关心 Service 对象的 IP 地址, 只需要知道它的名字, 就可以用 DNS 的方式去访问后端服务. 

比起 Docker, 这无疑是一个巨大的进步, 而且对比其他微服务框架（如 Dubbo、Spring Cloud), 由于服务发现机制被集成在了基础设施里, 也会让应用的开发更加便捷. 

Kubernetes 也为每个 Pod 分配了域名, 形式是 "IP 地址. 名字空间.pod.cluster.local", 但需要把 IP 地址里的 . 改成 - . 比如地址 10.10.1.87, 它对应的域名就是 10-10-1-87.default.pod

#### 让 Service 对外暴露服务

由于 Service 是一种负载均衡技术, 所以它不仅能够管理 Kubernetes 集群内部的服务, 还能够担当向集群外部暴露服务的重任. 

Service 对象有一个关键字段 "type", 表示 Service 是哪种类型的负载均衡. 前面我们看到的用法都是对集群内部 Pod 的负载均衡, 所以这个字段的值就是默认的 "ClusterIP", Service 的静态 IP 地址只能在集群内访问. 

除了 "ClusterIP", Service 还支持其他三种类型, 分别是 "ExternalName" "LoadBalancer" "NodePort". 不过前两种类型一般由云服务商提供, 我们的实验环境用不到, 所以接下来就重点看 "NodePort"这个类型. 

如果我们在使用命令 kubectl expose 的时候加上参数 --type=NodePort, 或者在 YAML 里添加字段 type:NodePort, 那么 Service 除了会对后端的 Pod 做负载均衡之外, 还会在集群里的每个节点上创建一个独立的端口, 用这个端口对外提供服务, 这也正是 "NodePort"这个名字的由来. 

修改 service.yml

```yaml

apiVersion: v1
kind: Service
metadata:
  name: ngx-svc

spec:
  selector:
    app: ngx-dep

  ports:
  - port: 80
    protocol: TCP
    targetPort: 80

  # 默认 ClusterIP
  #type: ClusterIP
  type: NodePort

```

创建并查看 service 对象
就会看到 "TYPE"变成了 "NodePort", 而在 "PORT"列里的端口信息也不一样, 除了集群内部使用的 "80"端口, 还多出了一个 "30651"端口, 这就是 Kubernetes 在节点上为 Service 创建的专用映射端口. 

```shell

kubectl get pod -o wide
# NAME                       READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
# ngx-dep-6796688696-klnmr   1/1     Running   0          2d16h   10.10.2.10   worker01   <none>           <none>
# ngx-dep-6796688696-rvd42   1/1     Running   0          2d16h   10.10.3.10   worker02   <none>           <none>

kubectl get svc
# NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
# kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        4d21h
# ngx-dep      ClusterIP   10.100.240.45   <none>        80/TCP         2d22h
# ngx-svc      NodePort    10.104.67.95    <none>        80:31652/TCP   2d16h

kubectl get service
# NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
# kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        4d21h
# ngx-dep      ClusterIP   10.100.240.45   <none>        80/TCP         2d22h
# ngx-svc      NodePort    10.104.67.95    <none>        80:31652/TCP   2d16h

kubectl get services
# NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
# kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        4d21h
# ngx-dep      ClusterIP   10.100.240.45   <none>        80/TCP         2d22h
# ngx-svc      NodePort    10.104.67.95    <none>        80:31652/TCP   2d16h

```

因为这个端口号属于节点, 外部能够直接访问, 所以现在我们就可以不用登录集群节点或者进入 Pod 内部, 直接在集群外使用任意一个节点的 IP 地址, 就能够访问 Service 和它代理的后端服务了. 

比如我现在所在的服务器是 "192.168.10.208", 在这台主机上用 curl 访问 Kubernetes 集群的两个节点 "192.168.10.210" "192.168.10.220", 就可以得到 Nginx Pod 的响应数据: 


```shell

curl 192.168.116.153:31652
# srv : 10.10.2.10:80
# host: ngx-dep-6796688696-klnmr
# uri : GET 192.168.116.153 /
# date: 2023-04-03T02:27:37+00:00

curl 192.168.116.154:31652
# srv : 10.10.3.10:80
# host: ngx-dep-6796688696-rvd42
# uri : GET 192.168.116.154 /
# date: 2023-04-03T02:27:42+00:00

```


### Ingress: 集群进出流量的总管

Service 对象, 它是 Kubernetes 内置的负载均衡机制, 使用静态 IP 地址代理动态变化的 Pod, 支持域名访问和服务发现, 是微服务架构必需的基础设施. 

Service 很有用, 但也只能说是 "基础设施", 它对网络流量的管理方案还是太简单, 离复杂的现代应用架构需求还有很大的差距, 所以 Kubernetes 就在 Service 之上又提出了一个新的概念: Ingress. 

#### 为什么要有 Ingress

通过上次课程的讲解, 我们知道了 Service 的功能和运行机制, 它本质上就是一个由 kube-proxy 控制的四层负载均衡, 在 TCP/IP 协议栈上转发流量（Service 工作原理示意图): 

https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/


但在四层上的负载均衡功能还是太有限了, 只能够依据 IP 地址和端口号做一些简单的判断和组合, 而我们现在的绝大多数应用都是跑在七层的 HTTP/HTTPS 协议上的, 有更多的高级路由条件, 比如主机名、URI、请求头、证书等等, 而这些在 TCP/IP 网络栈里是根本看不见的. 

Service 还有一个缺点, 它比较适合代理集群内部的服务. 如果想要把服务暴露到集群外部, 就只能使用 NodePort 或者 LoadBalancer 这两种方式, 而它们都缺乏足够的灵活性, 难以管控, 这就导致了一种很无奈的局面: 我们的服务空有一身本领, 却没有合适的机会走出去大展拳脚. 

Ingress, 意思就是集群内外边界上的入口, 在七层上做负载均衡

除了七层负载均衡, 这个对象还应该承担更多的职责, 也就是作为流量的总入口, 统管集群的进出口数据, "扇入" "扇出"流量（也就是我们常说的 "南北向"), 让外部用户能够安全、顺畅、便捷地访问内部服务（图片来源): 


#### 为什么要有 Ingress Controller

Ingress 可以说是在七层上另一种形式的 Service，它同样会代理一些后端的 Pod，也有一些路由规则来定义流量应该如何分配、转发，只不过这些规则都使用的是 HTTP/HTTPS 协议。

你应该知道，Service 本身是没有服务能力的，它只是一些 iptables 规则，真正配置、应用这些规则的实际上是节点里的 kube-proxy 组件。如果没有 kube-proxy，Service 定义得再完善也没有用。

同样的，Ingress 也只是一些 HTTP 路由规则的集合，相当于一份静态的描述文件，真正要把这些规则在集群里实施运行，还需要有另外一个东西，这就是 Ingress Controller，它的作用就相当于 Service 的 kube-proxy，能够读取、应用 Ingress 规则，处理、调度流量。

不过 Ingress Controller 要做的事情太多，与上层业务联系太密切，所以 Kubernetes 把 Ingress Controller 的实现交给了社区，任何人都可以开发 Ingress Controller，只要遵守 Ingress 规则就好。


Nginx 是开源的，谁都可以基于源码做二次开发，所以它又有很多的变种，比如社区的 Kubernetes Ingress Controller（https://github.com/kubernetes/ingress-nginx）、Nginx 公司自己的 Nginx Ingress Controller（https://github.com/nginxinc/kubernetes-ingress）、还有基于 OpenResty 的 Kong Ingress Controller（https://github.com/Kong/kubernetes-ingress-controller）等等。


#### 为什么要有 IngressClass

一个集群里有一个 Ingress Controller，再给它配上许多不同的 Ingress 规则，应该就可以解决请求的路由和分发问题了。

但随着 Ingress 在实践中的大量应用，很多用户发现这种用法会带来一些问题，

所以，Kubernetes 就又提出了一个 Ingress Class 的概念，让它插在 Ingress 和 Ingress Controller 中间，作为流量规则和控制器的协调人，解除了 Ingress 和 Ingress Controller 的强绑定关系。

现在，Kubernetes 用户可以转向管理 Ingress Class，用它来定义不同的业务逻辑分组，简化 Ingress 规则的复杂度。比如说，我们可以用 Class A 处理博客流量、Class B 处理短视频流量、Class C 处理购物流量。


```shell

kubectl api-resources | grep ingress
# ingressclasses               networking.k8s.io/v1       false        IngressClass
# ingresses        ing         networking.k8s.io/v1       true         Ingress

```

Ingress Controller 和其他两个对象不太一样，它不只是描述文件，是一个要实际干活、处理流量的应用程序，而应用程序在 Kubernetes 里早就有对象来管理了，那就是 Deployment 和 DaemonSet



```shell
# 生成 ingress 描述文件
export out="--dry-run=client -o yaml"
kubectl create ing ngx-ing --rule="ngx.test/=ngx-svc:80" --class=ngx-ink $out

```

--class，指定 Ingress 从属的 Ingress Class 对象。

--rule，指定路由规则，基本形式是“URI=Service”，也就是说是访问 HTTP 路径就转发到对应的 Service 对象，再由 Service 对象转发给后端的 Pod。

```yaml


apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ngx-ing
  
spec:
  # --class=ngx-ink
  ingressClassName: ngx-ink
  
  # --rule="ngx.test/=ngx-svc:80"
  # rules 把路由规则拆散了，有 host 和 http path，在 path 里又指定了路径的匹配方式，
  # 可以是精确匹配（Exact）或者是前缀匹配（Prefix），再用 backend 来指定转发的目标 Service 对象
  rules:
  - host: ngx.test
    http:
      paths:
      - path: /
        # 精确匹配（Exact）或者是前缀匹配（Prefix)
        pathType: Exact
        # backend 来指定转发的目标 Service 对象
        backend:
          service:
            name: ngx-svc
            port:
              number: 80

```



Ingress Class 本身并没有什么实际的功能，只是起到联系 Ingress 和 Ingress Controller 的作用，所以它的定义非常简单，在“spec”里只有一个必需的字段“controller”，表示要使用哪个 Ingress Controller

比如，如果我要用 Nginx 开发的 Ingress Controller，那么就要用名字“nginx.org/ingress-controller”：

```yaml
# ingress-class.yml

apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: ngx-ink

spec:
  controller: nginx.org/ingress-controller

```



把 ingress class 和 ingress 合并成一个 yml 文件



```yaml
# ingress.yml

# kubectl create ing ngx-ing --rule="ngx.test/=ngx-svc:80" $out
# kubectl create ing ngx-ing --rule="ngx.test/=ngx-svc:80" --class=ngx-ink $out

# https://docs.nginx.com/nginx-ingress-controller/

# curl 127.1/nginx-health
# curl 127.1:8081/nginx-ready

---
# ingress class
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: ngx-ink

spec:
  controller: nginx.org/ingress-controller

---

# ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ngx-ing

  # customize the behaviors of nginx
  annotations:
    nginx.org/lb-method: round_robin

spec:
  ingressClassName: ngx-ink

  rules:
  - host: ngx.test
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: ngx-svc
            port:
              number: 80
---


```

```shell

kubectl apply -f ingress.yml
# ingressclass.networking.k8s.io/ngx-ink created
# ingress.networking.k8s.io/ngx-ing created

kubectl get ingressclass
# NAME      CONTROLLER                     PARAMETERS   AGE
# ngx-ink   nginx.org/ingress-controller   <none>       11s

kubectl get ingress
# NAME      CLASS     HOSTS      ADDRESS   PORTS   AGE
# ngx-ing   ngx-ink   ngx.test             80      20s

# Ingress 对象的路由规则 Host/Path 就是在 YAML 里设置的域名“ngx.test/”，
# 而且已经关联了第 20 讲里创建的 Service 对象，还有 Service 后面的两个 Pod。
# 不要对 Ingress 里“Default backend”的错误提示感到惊讶，在找不到路由的时候，
# 它被设计用来提供一个默认的后端服务，但不设置也不会有什么问题，所以大多数时候我们都忽略它。
kubectl describe ing ngx-ing
# Name:             ngx-ing
# Labels:           <none>
# Namespace:        default
# Address:
# Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
# Rules:
#   Host        Path  Backends
#   ----        ----  --------
#   ngx.test
#               /   ngx-svc:80 (10.10.2.10:80,10.10.3.10:80)
# Annotations:  nginx.org/lb-method: round_robin
# Events:       <none>

```

#### Ingress Controller

Nginx Ingress Controller 的项目
https://github.com/nginxinc/kubernetes-ingress
因为它以 Pod 的形式运行在 Kubernetes 里，所以同时支持 Deployment 和 DaemonSet 两种部署方式。

这里选择的是 Deployment，相关的 YAML 也都在以下项目里复制了一份。
https://github.com/chronolaw/k8s_study/tree/master/ingress

Nginx Ingress Controller 的安装略微麻烦一些，有很多个 YAML 需要执行，但如果只是做简单的试验，就只需要用到 4 个 YAML：

```shell


kubectl apply -f common/ns-and-sa.yaml
kubectl apply -f rbac/rbac.yaml
kubectl apply -f common/nginx-config.yaml
kubectl apply -f common/default-server-secret.yaml

```

前两条命令为 Ingress Controller 创建了一个独立的名字空间“nginx-ingress”，还有相应的账号和权限，这是为了访问 apiserver 获取 Service、Endpoint 信息用的；后两条则是创建了一个 ConfigMap 和 Secret，用来配置 HTTP/HTTPS 服务。

部署 Ingress Controller 不需要我们自己从头编写 Deployment，Nginx 已经为我们提供了示例 YAML，但创建之前为了适配我们自己的应用还必须要做几处小改动：

- metadata 里的 name 要改成自己的名字，比如 ngx-kic-dep。
- spec.selector 和 template.metadata.labels 也要修改成自己的名字，比如还是用 ngx-kic-dep。
- containers.image 可以改用 apline 版本，加快下载速度，比如 nginx/nginx-ingress:2.2-alpine。
- 最下面的 args 要加上 -ingress-class=ngx-ink，也就是前面创建的 Ingress Class 的名字，这是让 Ingress Controller 管理 Ingress 的关键。

修改完之后，Ingress Controller 的 YAML 大概是这个样子：

```shell

apiVersion: apps/v1
kind: Deployment
metadata:
  name: ngx-kic-dep
  namespace: nginx-ingress

spec:
  replicas: 1
  selector:
    matchLabels:
      app: ngx-kic-dep

  template:
    metadata:
      labels:
        app: ngx-kic-dep
    ...
    spec:
      containers:
      - image: nginx/nginx-ingress:2.2-alpine
        ...
        args:
          - -ingress-class=ngx-ink

```

确认 Ingress Controller 的 YAML 修改完毕之后，就可以用 kubectl apply 创建对象：

```shell


kubectl apply -f kic.yml

```

注意 Ingress Controller 位于名字空间“nginx-ingress”，所以查看状态需要用“-n”参数显式指定，否则我们只能看到“default”名字空间里的 Pod：

```shell

kubectl get deploy -n nginx-ingress
kubectl get pod -n nginx-ingress

```

不过还有最后一道工序，因为 Ingress Controller 本身也是一个 Pod，想要向外提供服务还是要依赖于 Service 对象。所以你至少还要再为它定义一个 Service，使用 NodePort 或者 LoadBalancer 暴露端口，才能真正把集群的内外流量打通。这个工作就交给你课下自己去完成了。

这里，我就用第 15 讲里提到的命令kubectl port-forward，它可以直接把本地的端口映射到 Kubernetes 集群的某个 Pod 里，在测试验证的时候非常方便。

下面这条命令就把本地的 8080 端口映射到了 Ingress Controller Pod 的 80 端口：

```shell


kubectl port-forward -n nginx-ingress ngx-kic-dep-8859b7b86-cplgp 8080:80 &

```

我们在 curl 发测试请求的时候需要注意，因为 Ingress 的路由规则是 HTTP 协议，所以就不能用 IP 地址的方式访问，必须要用域名、URI。

你可以修改 /etc/hosts 来手工添加域名解析，也可以使用 --resolve 参数，指定域名的解析规则，比如在这里我就把“ngx.test”强制解析到“127.0.0.1”，也就是被 kubectl port-forward 转发的本地地址：

```shell

curl --resolve ngx.test:8080:127.0.0.1 http://ngx.test:8080

```

把这个访问结果和上一节课里的 Service 对比一下，你会发现最终效果是一样的，都是把请求转发到了集群内部的 Pod，但 Ingress 的路由规则不再是 IP 地址，而是 HTTP 协议里的域名、URI 等要素。

