## 移动端 python 爬虫笔记

- 学会 uiautomator2 对移动设备进行操控
- 学会抓包利器的使用
- 学会利用 python 编写移动端自动化抓取脚本
- 学会通过 atxserver2 监控多台移动设备


### requirements

```sh
pip install --upgrade pip setuptools wheel
pip install --upgrade uiautomator2 weditor
pip install ipython

```

https://github.com/openatx/uiautomator2
https://pypi.org/project/uiautomator2/

Python 3.6+ (社区反馈3.8.0不支持, 但是3.8.2支持）
pip install uiautomator2


QUICK START
先准备一台（不要两台）开启了开发者选项的安卓手机，连接上电脑，确保执行adb devices可以看到连接上的设备。

运行pip3 install -U uiautomator2 安装uiautomator2

运行 python3 -m uiautomator2 init 安装包含 httprpc 服务的 apk 到手机 +atx-agent, minicap, minitouch 

（在过去的版本中，这一步是必须执行的，但是从1.3.0之后的版本，当运行python代码u2.connect()时就会自动推送这些文件了）

命令行运行python打开python交互窗口。然后将下面的命令输入到窗口中。

```sh
import uiautomator2 as u2

d = u2.connect()  # connect to device

print(d.info)

```

```
In [1]: import uiautomator2 as u2

In [2]: d = u2.connect()
[W 200616 14:01:43 __init__:203] atx-agent has something wrong, auto recovering
[D 200616 14:01:43 __init__:286] device 127.0.0.1:62001 is online
[I 200616 14:01:43 init:155] uiautomator2 version: 2.9.6
[I 200616 14:01:43 init:352] Install minicap, minitouch
minitouch |⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿| 33.6 KB/33.6 KB
[I 200616 14:01:48 init:356] abi:x86 seems to be android emulator, skip install minicap
[I 200616 14:01:48 init:367] Install com.github.uiautomator, com.github.uiautomator.test 2.3.1
app-uiautomator.apk |⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿| 2.1 MB/2.1 MB
[I 200616 14:01:52 init:332] - app-uiautomator.apk installed
app-uiautomator-test.apk |⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿| 1.2 MB/1.2 MB
[I 200616 14:01:54 init:332] - app-uiautomator-test.apk installed
[I 200616 14:01:54 init:340] Install atx-agent 0.9.4
atx-agent_0.9.4_linux_386.tar.gz |⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿| 4.3 MB/4.3 MB
[I 200616 14:01:59 init:377] Check atx-agent version
Successfully init AdbDevice(serial=127.0.0.1:62001)

In [3]: d.info
[D 200616 14:02:50 __init__:595] kill process(ps): uiautomator
[D 200616 14:02:51 __init__:614] uiautomator-v2 is starting ... left: 40.0s
[D 200616 14:02:52 __init__:614] uiautomator-v2 is starting ... left: 39.0s
[D 200616 14:02:53 __init__:614] uiautomator-v2 is starting ... left: 38.0s
[D 200616 14:02:54 __init__:614] uiautomator-v2 is starting ... left: 37.0s
[D 200616 14:02:55 __init__:614] uiautomator-v2 is starting ... left: 35.9s
[D 200616 14:02:56 __init__:614] uiautomator-v2 is starting ... left: 34.9s
[D 200616 14:02:57 __init__:614] uiautomator-v2 is starting ... left: 33.9s
[I 200616 14:02:57 __init__:578] uiautomator back to normal
Out[3]:
{'currentPackageName': 'com.findtheway',
 'displayHeight': 1920,
 'displayRotation': 0,
 'displaySizeDpX': 540,
 'displaySizeDpY': 960,
 'displayWidth': 1080,
 'productName': 'MI 9',
 'screenOn': True,
 'sdkInt': 22,
 'naturalOrientation': True}
```

这时看到类似下面的输出，就可以正式开始用我们这个库了。因为这个库功能太多，后面还有很多的内容，需要慢慢去看 ....

{'currentPackageName': 'net.oneplus.launcher', 'displayHeight': 1920, 'displayRotation': 0, 'displaySizeDpX': 411, 'displaySizeDpY': 731, 'displayWidth': 1080, 'productName': 'OnePlus5', '
screenOn': True, 'sdkInt': 27, 'naturalOrientation': True}



先启动 adb-server, 再开启安卓模拟器, 否则可能会出现 adb devices 中无法找到 设备的问题
如何在启动安卓模拟器时, 自动启动 adb server
手机/模拟器中启动, 开发者选项, 启用 "USB调试", 


(app-2020) C:\Users\David>adb devices
* daemon not running; starting now at tcp:5037
* daemon started successfully
List of devices attached


(app-2020) C:\Users\David>adb devices
List of devices attached


(app-2020) C:\Users\David>adb devices
List of devices attached
127.0.0.1:62026 device

(app-2020) C:\Users\David>adb shell
root@shamu:/ # ps | grep atx
1|root@shamu:/ #


```sh
# 查看 ubuntu ssh 服务端
netstat -an | grep 22

# ens33, 网卡, 必须是开放的网络, 不能是需要登录验证的网络
ifconfig

```

adb 工具的使用

安装夜神模拟器, 
把 bin 目录添加到系统环境变量中 C:\develop\Nox\bin

```sh
adb devices
adb devices -l

# 关闭 adb 服务
adb kill-server
# 打开 adb 服务
adb start-server

adb devices

```


### cmder 安装与使用

- 官网地址: 
  - http://cmder.net/
- 参考: 
  - https://www.cnblogs.com/kinwing/p/11244943.html

- 介绍:
  - 下载的时候，会有两个版本，分别是mini与full版；唯一的差别在于有没有内建msysgit工具，这是Git for Windows的标准配备；全安装版 cmder 自带了 msysgit, 压缩包 23M, 除了 git 本身这个命令之外, 里面可以使用大量的 linux 命令；比如 grep, curl(没有 wget)； 像vim, grep, tar, unzip, ssh, ls, bash, perl 对于爱折腾的Coder更是痛点需求。

- 安装
  - 直接解压到某个目录就可以了，点击Cmder.exe运行。


- 配置环境变量
  - 在系统变量添加
    - 变量名： CMDER_HOME
    - 变量值： 安装绝对路径

  - 最后在Path添加一条斜体文字 %CMDER_HOME%

- 添加 cmder 到右键菜单
  - 配置环境变量后，在管理员权限的终端输入以下语句。
  - Win8或者Win10可以直接 win+x 再按 a 键进入。
    - Cmder.exe /REGISTER ALL

- 解决中文乱码问题
  - 之前在网找了好多方法，可是都解决不了，很多人在在Environment里添加 set LANG=zh_CN.UTF-8 来解决中文乱码的问题，可是我用这个方法并没有成功，可能是环境的原因吧，我的系统是win10的。
  - 最后找到解决办法：

    - Settings->Startup->Environment 添加
    - set LANG=zh_CN.UTF-8
    - set LC_ALL=zh_CN.utf8



### ubutun 安装 adb 工具

https://developer.android.com/studio/releases/platform-tools


```bash
# 下载
wget https://dl.google.com/android/repository/platform-tools-latest-linux.zip
wget https://dl.google.com/android/repository/platform-tools-latest-windows.zip


sudo wget -O /usr/local/sbin/adb https://raw.githubusercontent.com/NicolasBernaerts/ubuntu-scripts/master/android/adb
sudo wget -O /usr/local/sbin/fastboot https://raw.githubusercontent.com/NicolasBernaerts/ubuntu-scripts/master/android/fastboot

```


```bash
sudo apt install unzip
# 解压
sudo unzip -d /usr/local/sbin platform-tools-latest-linux.zip

sudo chmod +x /usr/local/sbin/platform-tools/adb /usr/local/sbin/adb
sudo chmod +x /usr/local/sbin/platform-tools/fastboot /usr/local/sbin/fastboot

```

```bash
# 修改系统环境变量
sudo vim /etc/profile

# 插入模式
o

export PATH=/usr/local/sbin:$PATH

source /etc/profile

```


#### ubuntu 中的 adb 连接 windows 中的 安卓模拟器

- windows 中启用 adb 的 tcpip 连接
  - 把 windows 默认的 usb 连接模式改为 tcpip 连接
  - adb tcpip 5555 

- 安卓模拟器设置网络为桥接模式
  - 查看ip地址: 172.17.100.15, 此 ip 是通过 nat 之后的地址, 无法连接
  - 设置 > 手机与网络 > 开户网络桥接模式
  - 查看ip地址: 192.168.1.8

- ubuntu 中操作

  - 查看 ubuntu 虚拟机的 ip, 确定与 安卓模拟器的 ip 在同一网段
  - 192.168.1.7

- ubuntu 中查看是否有设备连接

  - adb devices
  - adb connect 192.168.1.8
  - adb devices




mkdir --parent $HOME/.android
wget -O $HOME/.android/adb_usb.ini https://raw.githubusercontent.com/NicolasBernaerts/ubuntu-scripts/master/android/adb_usb.ini

sudo wget -O /etc/udev/rules.d/51-android.rules https://raw.githubusercontent.com/NicolasBernaerts/ubuntu-scripts/master/android/51-android.rules
sudo chmod a+r /etc/udev/rules.d/51-android.rules
sudo service udev restart


secureCRT 中 rz 打开文件发送对话框

安装非管理员版本

https://chocolatey.org/docs/installation#non-administrative-install
修改安装路径

choco install -y wget




删除 chocolate
https://chocolatey.org/docs/uninstallation

用户环境变量
ChocolateyLastPathUpdate

132358845815602296

系统环境变量

ChocolateyInstall
C:\ProgramData\chocolatey


Most of Chocolatey is contained in C:\ProgramData\chocolatey or whatever $env:ChocolateyInstall evaluates to. You can simply delete that folder.


电脑通过 usb 连接真实手机

电脑 > 右键 > 管理 > 设置管理器

如果通用串行总线控制器中一个带 ! 的 adb 设备, 就表示手机连接成功, 但是未安装驱动

查看是否连接成功
adb devices


#### 对手机进行初始化, 即在手机上安装 ui2 包

python -m uiautomator2 init


uiautomator-server 谷歌原生的 uiautomator

atx-agent: uiautomator 的守护进程

atx-agent 增加远程控制的功能, 依赖 minicap 和 minitouch 这两个工具

windows 中, 下载的文件保存在 C:\Users\David\.uiautomator2\cache 中
linux 中, 下载的文件保存在用户 home 目录下的 .uiautomator2 中


```
D:\David\Desktop\imooc_code\283_app_spider>workon app-2020
(app-2020) D:\David\Desktop\imooc_code\283_app_spider>python -m uiautomator2 init
[I 200606 13:34:36 init:155] uiautomator2 version: 2.9.5
[I 200606 13:34:36 init:352] Install minicap, minitouch
[D 200606 13:34:36 init:62] Download https://tool.appetizer.io/openatx/stf-binaries/raw/0.2.2/node_modules/minitouch-prebuilt-beta/prebuilt/x86/bin/minitouch
minitouch |⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿| 33.6 KB/33.6 KB
[?25h[D 200606 13:34:38 init:232] Push to /data/local/tmp/minitouch:0755
[I 200606 13:34:38 init:356] abi:x86 seems to be android emulator, skip install minicap
[D 200606 13:34:38 init:250] apk-debug package-info: None
[D 200606 13:34:38 init:251] apk-debug-test package-info: None
[I 200606 13:34:38 init:367] Install com.github.uiautomator, com.github.uiautomator.test 2.3.1
[D 200606 13:34:38 init:162] Shell: ('pm', 'uninstall', 'com.github.uiautomator')
[D 200606 13:34:38 init:162] Shell: ('pm', 'uninstall', 'com.github.uiautomator.test')
[D 200606 13:34:39 init:62] Download https://tool.appetizer.io/openatx/android-uiautomator-server/releases/download/2.3.1/app-uiautomator.apk
app-uiautomator.apk |⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿| 2.1 MB/2.1 MB
[?25h[D 200606 13:34:40 init:232] Push to /data/local/tmp/app-uiautomator.apk:0644
[D 200606 13:34:40 init:162] Shell: ('pm', 'install', '-r', '-t', '/data/local/tmp/app-uiautomator.apk')
[I 200606 13:34:41 init:332] - app-uiautomator.apk installed
[D 200606 13:34:41 init:62] Download https://tool.appetizer.io/openatx/android-uiautomator-server/releases/download/2.3.1/app-uiautomator-test.apk
app-uiautomator-test.apk |⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿| 1.2 MB/1.2 MB
[?25h[D 200606 13:34:42 init:232] Push to /data/local/tmp/app-uiautomator-test.apk:0644
[D 200606 13:34:42 init:162] Shell: ('pm', 'install', '-r', '-t', '/data/local/tmp/app-uiautomator-test.apk')
[I 200606 13:34:43 init:332] - app-uiautomator-test.apk installed
[I 200606 13:34:43 init:340] Install atx-agent 0.9.4
[D 200606 13:34:43 init:62] Download https://tool.appetizer.io/openatx/atx-agent/releases/download/0.9.4/atx-agent_0.9.4_linux_386.tar.gz
atx-agent_0.9.4_linux_386.tar.gz |⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿| 4.3 MB/4.3 MB
[?25h[D 200606 13:34:45 init:232] Push to /data/local/tmp/atx-agent:0755
[D 200606 13:34:47 init:162] Shell: ('/data/local/tmp/atx-agent', 'server', '--stop')
[D 200606 13:34:47 init:162] Shell: ('/data/local/tmp/atx-agent', 'server', '--nouia', '-d')
[D 200606 13:34:47 init:162] Shell: ('/data/local/tmp/atx-agent', 'server', '--nouia', '-d')
[I 200606 13:34:47 init:377] Check atx-agent version
[D 200606 13:34:47 init:390] Forward: local:tcp:8150 -> remote:tcp:7912
[D 200606 13:34:48 init:393] atx-agent version 0.9.4
Successfully init AdbDevice(serial=127.0.0.1:62026)

```

通过 uiautomator2 获取手机信息

通过 wifi 连接手机
通过 usb 连接手机
通过 tcpip 连接手机


wifi 连接时, 必须保证安卓模拟器, ubuntu 虚拟机, windows 在同一子网中, 彼此之间可以 ping 通


```python
import uiautomator2 as u2




```


ubuntu 安装 aapt 工具

sudo apt install aapt



服务相关和信息获取

atx agent 相关
但注意不能停止 atx-agent 服务, 否则, 就无法控制移动设备了

```bash
# launch atx-agent in daemon mode

adb shell /data/local/tmp/atx-agent server -d

# stop aleady running atx-agent and start daemon

adb shell /data/local/tmp/atx-agent server -d --stop

```




uiautomator api 接口


```python
import uiautomator2 as u2

device = u2.connect_usb("127.0.0.1:62001")
# 查看设备信息
device.info
# {'currentPackageName': 'com.vphone.launcher', 'displayHeight': 1920, 'displayRotation': 0, 'displaySizeDpX': 540, 'displaySizeDpY': 960, 'displayWidth': 1080, 'productName': 'sagit', 'screenOn': True, 'sdkInt': 22, 'naturalOrientation': True}

# 查看设备详细信息
device.device_info
# {'udid': 'android-08:00:27:11:1c:19-AOSP_on_Shama', 'version': '5.1.1', 'serial': 'android', 'brand': 'Android', 'model': 'AOSP on Shama', 'hwaddr': '08:00:27:11:1c:19', 'port': 7912, 'sdk': 22, 'agentVersion': '0.9.4', 'display': {'width': 1080, 'height': 1920}, 'battery': {'acPowered': False, 'usbPowered': True, 'wirelessPowered': False, 'status': 2, 'health': 2, 'present': True, 'level': 57, 'scale': 100, 'voltage': 3, 'temperature': 26, 'technology': 'Li-ion'}, 'memory': {'total': 3566136, 'around': '3 GB'}, 'arch': '', 'owner': None, 'presenceChangedAt': '0001-01-01T00:00:00Z', 'usingBeganAt': '0001-01-01T00:00:00Z', 'product': None, 'provider': None}

# 使用包名启动 app
device.app_start("com.findtheway")
# 使用包名关闭 app
device.app_stop("com.findtheway")

# 查看 uiautomator 是否在运行
device.service("uiautomator").running()
# 启动 uiautomator 服务
device.service("uiautomator").start()
# 关闭 uiautomator 服务
device.service("uiautomator").stop()
# 查看 atx-agent 的运行状态
# 如果 atx-agent 没有运行, 可以使用 u2.connect_usb 连接设备来启动 atx-agent 服务
device.agent_alive

# 查看手机的分辨率
device.window_size()
# 查看获取到的 wifi/ip 地址
# 注意: 和真机不同, 模拟获取到的 ip 地址是错误的, 是经过 nat 转换之后的 ip 地址, 
# 不是桥接端口获取到的真实 ip
# 如 获取到的是 172.17.99.15, 真机是 172.17.100.15
device.wlan_ip

```

通过 u2 来控制包的安装, 卸载等


```python
# coding=utf-8
import uiautomator2 as u2

# 通过 usb 连接安卓模拟器
device = u2.connect_usb("127.0.0.1:62026")

# 通过 app_install 方法安装 apk, data="xxx.apk"
device.app_install(data="http://file.mukewang.com/apk/app/117/imooc7.3.610102001android.apk")
device.app_install(data=r"D:\David\Desktop\imooc_code\283_app_spider\imooc7.3.610102001android.apk")
# [D 200606 14:52:13 __init__:1272] pm install -rt /data/local/tmp/_tmp.apk

# 启动 app
# app_start(package_name: str, activity: Optional[str]=None, wait: bool = False, stop: bool=False, use_monkey: bool=False)
device.app_start(package_name="cn.com.open.mooc")

# 获取当前前台运行的 app 的信息
# dict(package, activity, pid?)
device.app_current()
# {'package': 'cn.com.open.mooc', 'activity': 'cn.com.open.mooc.component.user.activity.login.LoginActivity'}

# 关闭 app
# app_stop(self, package_name)
device.app_stop("cn.com.open.mooc")

# 获取已安装的 app 的详细信息
device.app_info(package_name="cn.com.open.mooc")
# {'packageName': 'cn.com.open.mooc', 'mainActivity': 'com.imooc.component.imoocmain.splash.MCSplashActivity', 'label': '慕课网', 'versionName': '7.3.6', 'versionCode': 7360, 'size': 33342982}

# 清除 app 缓存
# 尤其是我们后面要进行的视频数据抓取，会产生一定的缓存
device.app_clear("cn.com.open.mooc")

# 卸载 app
device.app_uninstall("cn.com.open.mooc")

# oppo 应用商店
device.app_current()
# {'package': 'com.oppo.market', 'activity': 'a.a.a.agk'}

device.app_info(package_name="com.oppo.market")

# {'packageName': 'com.oppo.market',
#  'mainActivity': 'com.oppo.market.activity.MainActivity',
#  'label': '软件商店',
#  'versionName': '7.9.0',
#  'versionCode': 7902,
#  'size': 22697653}

device.app_clear("com.oppo.market")

# vivo 应用商店

device.app_current()
# {'package': 'com.bbk.appstore', 'activity': 'com.bbk.appstore.ui.AppStore'}

device.app_info(package_name="com.bbk.appstore")

# {'packageName': 'com.bbk.appstore',
#  'mainActivity': 'com.bbk.appstore.ui.AppStore',
#  'label': '應用程式商店',
#  'versionName': '8.18.2.0',
#  'versionCode': 9220,
#  'size': 17697711}

# 获取所有 app 列表
device.app_list()
# ['com.github.uiautomator', 'com.android.providers.telephony', 'com.android.providers.calendar', 'com.android.providers.media', 'com.android.wallpapercropper', 'com.android.documentsui', 'com.android.externalstorage', 'com.android.htmlviewer', 'cn.com.open.mooc', 'com.android.mms.service', 'com.android.providers.downloads', 'com.github.uiautomator.test', 'com.android.browser', 'com.android.soundrecorder', 'com.android.defcontainer', 'com.android.providers.downloads.ui', 'com.android.pacprocessor', 'com.android.certinstaller', 'android', 'com.android.contacts', 'com.android.camera2', 'com.android.backupconfirm', 'com.android.provision', 'com.android.phasebeam', 'com.cl.newt66y', 'com.android.providers.settings', 'com.android.sharedstoragebackup', 'com.android.dreams.basic', 'com.android.inputdevices', 'com.estrongs.android.pop', 'com.hipu.yidian', 'com.google.android.webview', 'com.findtheway', 'com.android.onetimeinitializer', 'com.android.server.telecom', 'com.android.keychain', 'com.android.chrome', 'com.android.inputservice', 'com.android.gallery3d', 'com.android.packageinstaller', 'com.svox.pico', 'com.android.proxyhandler', 'com.cyanogenmod.filemanager', 'com.android.managedprovisioning', 'com.android.wallpaper.livepicker', 'com.android.settings', 'com.android.vpndialogs', 'com.android.phone', 'com.android.shell', 'com.android.providers.userdictionary', 'com.android.location.fused', 'com.android.systemui', 'com.android.bluetooth', 'com.android.providers.contacts', 'com.android.captiveportallogin']

# 获取所有正在运行的app的列表
device.app_list_running()
# ['com.android.externalstorage', 'cn.com.open.mooc', 'com.android.chrome', 'com.android.inputservice', 'com.android.settings', 'com.android.keychain', 'com.android.phone', 'com.android.documentsui', 'com.svox.pico', 'com.github.uiautomator', 'com.estrongs.android.pop', 'com.android.defcontainer', 'com.hipu.yidian', 'com.android.systemui']

# 停止所有 app, 关闭所有除了 u2 之外的所有 app
device.app_stop_all()
# 卸载所有 app, 卸载所有第三方 app, u2 项目包不会卸载 'pm', 'list', 'packages', '-3
device.app_uninstall_all()


```


app activate 控件

TextView 显示文字
EditText 输入框, 可编辑
ImageView 显示图片
Button 按钮
CheckBox 复选框
RadioButton 单选按钮


Android基础--------Android常用控件介绍及使用
https://blog.csdn.net/weixin_38423829/article/details/80566203

控件

TextView 显示文字，相当于Panel
ImageView 显示图片
EditText 输入框，可编辑,可设置软键盘方式
Button 按钮，可附带图片
CheckBox 复选框
RadioButton 单选按钮（和 RadioGroup 配合使用）

按用途分类：

文本控件
– TextView
– EditText

按钮控件
– Button
– ImageButton

状态开关按钮
– ToggleButton

单选与复选按钮
– CheckBox 和 RadioButton

图片控件
– ImageView

时钟控件
– AnalogClock
– DigitalClock

日期与时间选择控件
– DatePicker
– TimePicker


weditor
https://pypi.org/project/weditor/
https://github.com/openatx/weditor

pip install weditor
pip freeze | grep weditor
pip freeze | findstr weditor
weditor==0.5.0

where weditor
D:\David\envs\app-2020\Scripts\weditor.exe



```bash
# 查看 atx-agent 是否处理启动状态
adb shell

ps | grep atx-agent

# 手动启动 atx-agent
cd /data/local/tmp

ll

# 启动 atx-agent

./atx-agent server -d

```


UiSelector 和 控件定位

UiSelector


Text 文本选取方式

text 全文本匹配
textContains 文本包含
textMatches 正则表达式
textStartsWith 起始文本


```bash
weditor
listening on http://172.18.15.224:17310

```



### windows appium 命令行版本安装
D:\David\Desktop\imooc_code\283_app_spider\Android SDK Command line tools运行sdkmanager报告Warnin.txt


#### 安装 java

##### java jre 下载

选择语言英语, 所有下载, 下载离线安装包
https://www.java.com/en/download/manual.jsp

##### java jdk 下载
底部导航 Developers > JDK downloads > Java SE 8u211 / Java SE 8u212 > JDK download > 
Windows x86	202.62 MB  	jdk-8u211-windows-i586.exe

Windows x64	215.29 MB  	jdk-8u211-windows-x64.exe


注: java jdk需要在注册并登录账号后才能下载

##### 安装

1. 新建c:/java/jdk, c:/java/jre, 分别安装到 jdk 和 jre 到这两个目录中

##### 环境变量配置

新建
变量名: JAVA_HOME
变量值: C:\java\jdk

新建
变量名: ClassPath
变量值: .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
注意上边最开始有个点


添加
变量名: Path
变量值: 
%JAVA_HOME%\bin
%JAVA_HOME%\jre\bin

或者使用绝对路径

C:\java\jdk\bin
C:\java\jdk\jre\bin


确定已经安装完成

命令行分别输入: java, javac, java -version, javac -version 进行测试

#### 安装 Android Stuido 或 Android SDK

注意: AndroidStudio安装包 和 SDK解压包 二者选其一安装
若用到开发则安装Android Studio, 若用不到开发, 则安装 SDK


2.安装 Android SDK 工具

1. 下载地址: https://www.androiddevtools.cn/
2. 依次点击 Android SDK 工具 >> SDK Tools >> 会跳转到以下界面，Windows建议选择.exe后缀, installer_r24.4.1-windows.exe

安装 installer_r24.4.1-windows.exe, 安装到 C:\Android\android-sdk 中

双击打开 "SDK Manager.exe", 安装必备包, 包括 SDK Tools, Platform-tools, Build-tools, Extras

检查对应的目录信息应该包含了以下几个

build-tools
platform-tools
tools

其中，build-tools包含不同版本的安卓版本的工具，每个版本一个文件夹，配置的时候将版本号文件夹配置上类似于：build-tools/xxx
platform-tools存放着adb等工具的
tools存放着 uiautomatorviewer 等工具


安装完成后配置环境变量，新增系统变量 ANDROID_HOME，值就是安装的那个文件夹

新建: ANDROID_HOME
值: C:\Android\android-sdk

系统环境变量 Path 中添加

%ANDROID_HOME%\build-tools
%ANDROID_HOME%\platform-tools
%ANDROID_HOME%\tools

```sh
# 检查Android SDK是否安装成功
adb --version
```

##### 升级 platform-tools 到最新版


官方下载安装 platform-tools, 其中包含 adb.exe, aapt.exe 工具
https://developer.android.com/studio/releases/platform-tools

或者
https://dl.google.com/android/repository/platform-tools-latest-windows.zip

参考:
https://github.com/codepath/android_guides/wiki/Installing-Android-SDK-Tools

解压到 D:\Android\android-sdk 中, 覆盖原 platform-tools 中的所有文件


6.安装 node.js

为何安装Node.js
Node.js的软件包生态系统 npm 是全球最大的开源库生态系统，Appium 正是使用 npm 命令安装。


下载安装Node.js
官网下载安装包，推荐下载 LTS 版本：https://nodejs.org/zh-cn/download/
注意：不要使用 root 权限安装

配置环境变量-系统变量
安装时已默认选择“Add to PATH”，检查系统环境变量 Path 是否新增 Node.js 安装路径，如果没有则添加。

PATH

C:\Program Files\nodejs

检查 Node.js 是否安装成功

node -v
npm -v


然后要配置npm参数，我使用的默认安装路径，如果修改了安装路径就要把命令当中的路径换成实际路径，打开cmd，操作如下: 

npm config set prefix="C:\\Program Files\\nodejs\\node_global"
npm config set cache="C:\\Program Files\\nodejs\\node_cache"
npm config set init-module="C:\\Program Files\\.npm-init.js"
npm config set registry="https://registry.npm.taobao.org/"
npm config set tmp="C:\\Program Files\\Temp"
npm config set userconfig="C:\\Program Files\\.npmrc"


1.2.1 安装Appium

方法一
官方文档的安装方式（因为服务器在海外，没翻墙基本安装不上）：
npm install -g appium

方法二
设置淘宝镜像源
npm config set registry https://registry.npm.taobao.org

安装appium
npm install -g appium

方法三
先安装cnpm, 淘宝源 再安装 appium

npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install -g appium  #全局安装appium


```sh
# 安装指定版本
cnpm install appium@1.13.0 -g

# 检查 Appium 是否安装成功
appium -v

# 通过 cnpm 安装 appium-doctor，并使用 appium-doctor 检查 appium 整体环境
# 安装appium-doctor
cnpm install -g appium-doctor
#检查环境
appium-doctor

# 安装 python 客户端
pip install Appium-Python-Client

# 查看连接的设备
adb devices
adb devices -l

# 启动Appuim
appium

```

##### 运行测试代码
APP名:西五街

```python

from appium import webdriver
desired_caps = {}
desired_caps['platformName'] = 'Android'  # 系统名
desired_caps['platformVersion'] = '5.1.1' # 系统版本 模拟器5.1.1   真机 6.0.1
desired_caps['deviceName'] = 'emulator-5554' # 手机型号或者模拟器型号
desired_caps['appPackage'] = 'com.liulianp.android' # app的包名 
desired_caps['appActivity'] = 'com.liulianp.android.module.welcome.SplashActivity' # app程序主入口名
desired_caps['unicodeKeyboard'] = True
desired_caps['resetKeyboard'] = True

#启动appium-desktop服务器，服务器IP根据实际填写
driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', desired_caps)

```


2.夜神模拟器 下载并安装夜神模拟器 https://www.yeshen.com/


先确保你的电脑 Hyper-V 是关闭的，否则启动模拟器时部分电脑会蓝屏重启。
把安卓sdk中的adb.exe复制两份，其中一个改名为nox_adb.exe后，放到夜神模拟器安装目录的bin目录下覆盖原文件

安装完成--进入到你安装的bin目录下，输入: nox_adb.exe connect 127.0.0.1:62001


windows上连接mumu模拟器不能自动识别，需要手动输入 下面的连接命令：

adb connect 127.0.0.1:7555

如果没有识别，需要把 adb server 杀掉重新执行 adb devices，

adb kill-server




##### appium 连接多个安卓模拟器

```bash
# 使用不同的端口和
appium -p 4723 -U 127.0.0.1:62025
appium -p 4725 -U 127.0.0.1:62026

```

Installing Android SDK Tools

https://github.com/codepath/android_guides/wiki/Installing-Android-SDK-Tools

Android SDK Command line tools运行sdkmanager报告Warning: Could not create settings错误信息解决方法
https://blog.csdn.net/zhufu86/article/details/106747556/


https://stackoverflow.com/questions/4681697/is-there-a-way-to-automate-the-android-sdk-installation/4682241


https://developer.android.com/studio

https://www.androiddevtools.cn/

https://developer.android.com/studio/intro/update

http://appium.io/docs/en/about-appium/getting-started/

https://github.com/appium/appium-desktop/releases

启动多个appium服务(同时运行多台设备)
https://www.cnblogs.com/may18/p/10577074.html
appium实现多个模拟器同时启动Demo
https://blog.csdn.net/weixin_41080927/article/details/103089352
appium开启两个服务端口链接两个模拟机，但每次都只运行一台设备。终于解决。
https://blog.csdn.net/biuAimee/article/details/86579277
Appium命令行方式启动服务
https://www.jianshu.com/p/5c823b17f9d2
启动多个appium服务(同时运行多台设备)
https://www.cnblogs.com/may18/p/10577074.html

Parallel Android Tests
https://dpgraham.github.io/appium-docs/setup/parallel_tests/