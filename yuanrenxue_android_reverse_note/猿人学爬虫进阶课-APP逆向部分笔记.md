猿人学爬虫进阶课APP部分讲义

## 一, 安卓环境, 工具使用, 安卓APP代码结构

### 课程前言介绍

- sec2 frida技术 脱壳 抓包 各种定位分析方法 ida so分析, 需root手机, 需解决反框架, 反设备等问题
- sec6 基于平头哥 脱壳 抓包 定位技巧 sekrio 类xposed插件开发 设备指纹擦除 容器化技术 RPC 群控, 不需root手机, 暂无需解决反框架, 反设备等问题, 需要改包, 注入smali代码
- 通过在实操中, 熟悉常用的工具和明白常用套路, 常用分析方法, 并在熟悉和练习的过程中做到对安卓系统和安卓系统类的了解. 

### 环境准备

#### 安装JAVA: 反编译工具依赖java

- java官方下载地址
  - https://www.oracle.com/technetwork/java/javase/downloads/index.html

- 注意
  - 安装完必须设置java环境变量
  - 检查安装成功
    - java -version
    - javac -version
  - 手机一台: nexus 6p (刷机 root) 找商家刷系统, 

  - 前期用网易mumu模拟器代替练习 https://mumu.163.com/
  - nox player https://www.bignox.com/ > android 5 + root + xposed 
  - 电脑内存越大越好

### apk包文件结构

- `.apk` 文件, 一种压缩包, 直接使用 7z 打开, 包含以下文件/目录

  - classes.dex
    - 编译后的 java 代码

  - AndroidManifest.xml
    - 重要配置文件, APP启动入口, 界面信息等等
    - 直接解压出来是乱码

  - assets
    - 没被编译的 静态资源目录

  - lib
    - so文件, C/C++ 编译成 so 文件

  - res
    - 被编译了的资源目录

  - resources.arsc
    -资源文件索引

- 安卓虚拟机 davlink虚拟机 (安卓4)  art虚拟机 (安卓5以后) , java > dex 文件


### 工具使用

#### adb 使用

- 需要单独下载 (PS: 模拟器, androidstudio里自带) 

- 文档
  - https://github.com/mzlogin/awesome-adb
  - https://blog.csdn.net/weixin_43847093/article/details/84673414
  - https://developer.android.com/studio/command-line/adb

- 常用命令

  - adb shell 进入手机
  - adb install xxx.apk 安装apk
  - adb install -r xxx.apk 覆盖安装 
  - adb uninstall 包名 卸载
  - adb push 电脑端源文件路径 手机端目标路径, 
    - 最好把文件放在/sdcard/ 目录下面
  - adb pull 手机端源文件路径 电脑源文件路径 
  - adb logcat 查看日志

#### 反编译工具

- apktool
  - https://ibotpeaches.github.io/Apktool/

  - 反编译apk包
    - apktool d test.apk -o test
    - apktool d com.dahuodong.veryevent.apk
    - smalli/xxx/xxx.smali 文件, 反编译的 classes.dex 中的代码得到的汇编文件
    - AndroidManifest.xml apktool 解压出来的 AndroidManifest.xml 是可读的
    - 包含 `<action android:name="android.intent.action.MAIN"/>` 的 activity 是 app 加载的第一个界面

  - 重打包成apk
    - apktool b folder_name -o demo_unsigned.apk
    - 会在原 folder_name/dist 目录中生成 apk 文件

  - 优点: 可以重打包apk
  - 缺点: 只能使用命令, 没有可视化操作界面, 不方便分析代码, 反编译 (打包) 近一两年的 apk, 很容易报错


- jadx 使用

  - https://github.com/skylot/jadx
  - 用最新的, 少bug, 内存使用少
  - 优点: 可视化做得很好, 搜索代码方便, 容易上手
  - 缺点: 只能查看, 不能修改代码, 反混淆功能一般

- jeb 
  - https://www.pnfsoftware.com/jeb/
  - https://down.52pojie.cn/Tools/Android_Tools/
  - jeb 使用时间到期, 解决方法: 拷贝 jeb_3_1_9_patch 文件夹 (课程云盘) 里所有覆盖 jeb 原来的
  - 优点: 反编译比 jadx 强大, 能动态调试 smali, 可视化, 反混淆功能比jadx强

- android killer

### APP 代码结构

- 框架代码
- 自己代码
- smali:
  - smali一种类似汇编格式
  - apk 反编译代码成smali文件
    - apktool d xxx.apk
  - 可以修改/增删smali代码, 重打包成apk后执行
  - 结合java代码看smali代码
  - dex 转 smali
    - java -jar baksmali.jar d classes.dex

### Android app 启动顺序

- APP简单启动顺序
  - 点击APP启动 ---> 执行 application(oncreate方法) ------> 执行 main activity(oncreate方法) ----> 启动成功

- APP启动阶段可能会做的检测
  - 签名校验
  - root检测


### apktool 使用问题解决

- 1.Not a valid dex magic value
  - apktool d com.douguo.recipe_6.9.79.2_570.apk --only-main-classes
  - assets 目录中有一个 dex 文件, 是不规范的, 无法解析

- 2.重新打包时报: Not a PNG file
  - 用 file 命令查看文件类型, 修改成正确的, 重新打包, 或者修改为 jpg 格式的图片
  - 如 com.iCitySuzhou.suzhou001_8.2_54

- 3.重新打包报: error: No resource identifier found for attribute 'xxxxxx' in package

apktool d -r xxx.apk
jadx 打开xxx.apk 看 AndroidManifest
apktool b xxx


### 4.使用 sec6 讲的 ratel

PS: 逆向工具本身的局限性和Bug

-------------

### APP代码结构

- 业务模块
- 系统模块: com.android.
- 第三方模块: 网络请求 okhttp, 支付 pay, 推送 umeng 等等

### APP简单启动顺序

点击APP启动 ----> 执行Application(attach, onCreate方法) ----> 执行开屏界面 Activity ----> 执行Main Activity (onCreate方法) ----> 启动成功


### Activity 介绍

- 拓展阅读
  - https://developer.android.google.cn/
  - https://developer.android.com/guide/components/activities/intro-activities


### 简单 APP 签名校验

#### apk 打包与签名

- 重新打包
  - apktool b xxx -o xxx-unsigned.apk

- 签名作用
  - 未签名APK不能在安卓手机上安装
  - APP在启动时会对签名校验: 重打包的APP可能需要逆向, 跳过校验

- 签名工具
  - JAVA JDK 自带的 keytool jarsigner

- 生成证书

```sh

keytool -genkey -keystore my-release-key.keystore -alias my_alias -keyalg RSA -keysize 4096 -validity 10000

```

- 用证书给 apk 签名

```sh

jarsigner -sigalg MD5withRSA -digestalg SHA1 -keystore my-release-key.keystore -signedjar xxx.signed.apk xxx.unsigned.apk my_alias

```

## 二 Frida HOOK 基础介绍

- 安卓逆向分析方法
  - 1. 根据现象正向分析代码流程. 较难
  - 2. 搜索大法
  - 3. hook系统类/方法, 打印堆栈, 分析调用堆栈, 定位代码

- HOOK
  - 在系统没有调用该方法 (函数) 前, 获得该方法 (函数) 的控制权, 从而改写该方法. 

- 借助Hook框架完成Hook
  - Frida,  Xposed,  平头哥 等等. 

- Frida
  - Hook 支持 安卓 iOS windows linux, 支持 Hook 语言 ( javascript c swift) , hook (java,so)无需安装

- Xposed
  - Hook 安卓, Java语言, 需要安装在手机上, hook java, 需要 root

- 平头哥
  - Hook 安卓, 无需root Java语言, 被检测少, 做了设备与指纹对坑, 支持安卓 9 10 
  - sec6讲
  - 降低逆向难度, 减轻代码分析难度, 提高分析效率, 逆向很重要的辅助手段


### Frida

- https://frida.re/docs/functions/
- 准备: 一台root的安卓手机, 演示程序在安卓6和8中测试通过. 

#### Frida 组成部分

  - Frida-server
    - 运行在手机上. 安卓 6 使用 frida-server-12.8.10, 安卓 8 以上手机使用较新的 frida-server 版本

  - Frida
    - Python 模块

  - Frida-tools
    - 提供 cli 工具命令, 与 Frida-server 进行交互, 如 frida-ps frida-trace

#### Frida 配置

- 1. 下载 frida-server 解压 push 到手机 增加可执行权限并运行
  - https://github.com/frida/frida/releases
  - android 手机根据不同的处理器下载 frida-server-xxx.xxx.xxx-android-xxx.xz

```sh

# nox 的 sdcard 为只读的, 无法添加 x 可执行权限
adb push frida-server /data/local/tmp/
adb shell
su
cd /data/local/tmp

# 确定手机当前用户是root用户或拥有root权限
chmod 777 frida-server
# 启动 frida-server
./frida-server

```

frida-server 报错. nox + android5 + frida-server-14.2.14-android-x86
```
/frida-server-14.2.14-android-x86                                             <
{"type":"error","description":"TypeError: r.art::mirror::Object::Clone is not a function","stack":"TypeError: r.art::mirror::Object::Clone is not a function\n    at Bt (frida/node_modules/frida-java-bridge/lib/android.js:1183:1)\n    at Vt.replace (frida/node_modules/frida-java-bridge/lib/android.js:1028:1)\n    at Function.set [as implementation] (frida/node_modules/frida-java-bridge/lib/class-factory.js:1010:1)\n    at Function.set [as implementation] (frida/node_modules/frida-java-bridge/lib/class-factory.js:925:1)\n    at installLaunchTimeoutRemovalInstrumentation (/internal-agent.js:249:24)\n    at init (/internal-agent.js:33:3)\n    at c.perform (frida/node_modules/frida-java-bridge/lib/vm.js:11:1)\n    at v._performPendingVmOps (frida/node_modules/frida-java-bridge/index.js:238:1)\n    at frida/node_modules/frida-java-bridge/index.js:213:1\n    at c.perform (frida/node_modules/frida-java-bridge/lib/vm.js:11:1)","fileName":"frida/node_modules/frida-java-bridge/lib/android.js","lineNumber":1183,"columnNumber":1}

```

- 2. 电脑上安装 frida python 包

https://pypi.org/project/frida/#files

- 注意要保证 frida python 包与 frida-server 的版本相一致

```sh

pip install frida
pip install frida-tools

```

- 3. 测试安装

  - 电脑上运行 `frida-ps -U`
  - 出现 `android process` 列表表示成功

  - windows 运行 端口转发到 PC, 如果使用 wifi 连接到手机上的 frida-server 时报错, 就要进行端口转发
    - adb forward tcp:27043 tcp:27043
    - adb forward tcp:27042 tcp:27042

#### Frida API 介绍

- https://frida.re/docs/javascript-api/
- Python + Javascript

  - Python 代码作用: 控制, 写法固定, 负责跟 frida-server 通信, 把 JS 代码传递给 frida server
  - Javascript 代码作用: Hook 操作

- Frida 常用 Javascript API

  - Java 模块
    - Hook Java 层的类方法相关
    - https://frida.re/docs/javascript-api/#java
  - Process 模块
    - 处理当前线程相关
  - Interceptor 模块
    - 操作指针相关, 多用来Hook Native 相关
  - Memory 模块
    - 内存操作相关
  - Module 模块
    - 处理 so 相关

#### frida 基本使用代码演示

- python 代码调用 js

- 不使用 python, frida 直接 frida 直接调用 js 代码
  - without reboot
    - frida -U APP包名 -l js脚本名
    - frida -U com.example.seccon2015.rock_paper_scissors -l s2_l2_hook_rps.js
  - with reboot
    - frida -U -l xxx.js -f com.xx.xx --no-pause
    - frida -U -l s2_l2_hook_rps.js -f com.example.seccon2015.rock_paper_scissors --no-pause

- 分析 rps-frida-test.apk 的签名校验

```js

Java.perform(
  function () {
  //hook 代码
  }
)

```

Java.use() 的使用
Java.enumerateClassLoaders(callbacks) 遍历 classloader

- 两种启动hook方式

  - 1. attach 进程名 : APP启动后再hook, 不能hook app启动阶段
  - 2. spawn: 重启APP, 适合hook app 启动阶段

- frida Hook Java 类 Tips

  - 访问成员变量写法
    - this.成员变量名.value

  - hook匿名类写法
    - Java.use('类$类') smali文件里找

  - 从匿名类/内部类访问外部类的属性写法
    - this.this$0.value.外部类的属性名.value

  - 新建一个对象写法 new
    - 类.方法名.$new(参数)

  - 重载写法
    - 类.方法名.overload(参数1, 参数2.......).implementation

  - hook 构造方法
    - 类.$init().implementation

  - 接口继承实现

  - 类型转换

    - 用法例
    - 把 P 转成 Map 类型
    - var Map = Java.use('java.util.Map')
    - var NewP = Java.cast(P, Map)

  - 打印 object object 的解决方法

    - 方法一
      - 1. 先确认object是什么类型(比如要打印p) 先console.log(p.$className) 查看p是什么数据类型
      - 2. Java.cast 把p强转为对应类型
      - 3. 调用该类对应的输出方法. 通常有一个toString()方法

    - 方法二
      - 使用js里的JSON类
      - 尝试 console.log(JSON.stringify(p))
      - 可能打印不出来字符串, 一般能打印出p的字节数组.  (可以用着你的数据和真实数据的对比) 

  - bytes array 是 object
    - String 和 bytes array可以相互转化
    - String.getBytes() 字符串转bytes array
    - new String(bytes) bytes转成字符串. PS: 本身如果是不可打印字符串, 打印是乱码

  - 请求参数在jadx中搜索不到
    - 可能在so中
    - 可能是动态加载jar, apk, dex
    - 可能对字符串有编码(base64 unicode等等)或加密
    - 可能在资源文件里
    - 可能是内嵌浏览器webview, 代码在JS中或由服务器返回

    - 解决方法
      - 1. 不定位参数, 先找到url 顺着URL往下跟踪, 借助hook
      - 2. 盲狙, 大范围hook, hook加密类
      - 3. 从activity, 从那儿顺着逻辑跟踪, 借助hook

  - jadx中有代码, 但Hook不到类

    - 可能是动态加载 dex, 类可能在另外的 classloader 中, 需要切换 classloader 再 hook classloader 概念普及 https://www.jianshu.com/p/96a72d1a7974

  - hook加密类
    - 先总结各加密类的用法, key iv 明文 密文等是如何获取的, 再hook对应的类和方法
    - AES https://www.cnblogs.com/widgetbox/p/11611201.html
    - RSA https://blog.csdn.net/qq_22075041/article/details/80698665
    - DES https://www.jianshu.com/p/bf6b4afaf41e

  - MD5 SHA等摘要算法 
    - https://blog.csdn.net/baidu_34045013/article/details/80687557
  - HMAC摘要算法
    - https://blog.csdn.net/cdzwm/article/details/6973345

  - 抽象方法

  - 接口类

  - hook so

  - hook jni

  - frida choose

  - frida rpc: 远程主动调用安卓代码

```py
test='''
rpc.exports = {
    // PS: 函数名不要大写
    testfunc: function (str) {
        var enc = ''
        Java.perform(function () {
            var a = Java.use("XXXXXXXXXXX");
            enc = a.a(str)
        });
        return enc
    }
}
'''
```

  - frida不使用python, 直接使用js脚本用法

  - 不重启
    - frida -U APP包名 -l js脚本名:  frida -U com.xxx.xxx -l xxx.js
  - 重启
    - frida -U -l xxx.js -f com.xx.xx --no-pause

  - hook写法很简单, 重要的是找到Hook点
  - 借助hook手段, 验证分析思路或得到更多线索

- Jeb分析代码: 可以反混淆字符串
  - 实践: 结合frida hook分析简单签名校验
  - wxy

- java概念介绍--反射调用

  - 反射调用好处
    - 屏蔽实现细节
    - 调用私有方法
    - 代码特征检测

  - Class类

```java

obj.getClass().getMethod('方法名字符串', new Class[0]).invoke(obj, new Object[0]);

obj.getClass().getMethod(str, new Class[0]).invoke(obj, new Object[0]);

context.getClass().getMethod('getPackageManager', new Class[0]).invoke(context, new Context[0]);
// 相当于
context.getPackageManager()

```

## frida 案例

### 破解app简单逻辑



### app 签名校验

- 签名校验一般在APP启动阶段
  - 使用不同的签名重新签名 app 时, APK signature 中的 md5, sha1, sha256 的值就会发生变化

- 签名关键字, Signature 类

  - context.getPackageManager().getPackageInfo(context.getPackageName(), 64).signatures[0].hashCode()
  - 通过上下文 context 获取包管理器 getPackageManager, 通过包管理器 PackageManager 获取包信息 getPackageInfo, 取包信息 PackageInfo 中的 .signature 属性, 再获取 signature 的 hash 信息

- Application, 类加载器简单初始化过程
  - 拓展阅读 https://www.jianshu.com/p/8742dbf5ec3a

- Context 类
  - 上下文信息, app运行环境信息
  - android.content.Context: 保存应用环境信息
  - Context 拓展阅读

    - https://www.jianshu.com/p/0599b060e074
    - https://blog.csdn.net/bendan50/article/details/80142612
    - https://developer.android.google.cn/reference/android/content/Context

- PackageManager 类: 管理安卓系统信息和APP的信息
  - 可以获取 app 信息, 版本信息等
  - android.content.pm.PackageManager: 包管理器
  - PackageManager 拓展阅读

    - https://www.cnblogs.com/travellife/p/3932823.html
    - https://developer.android.google.cn/reference/android/content/pm/PackageManager

- Signature 拓展阅读
  - https://developer.android.google.cn/reference/android/content/pm/Signature


#### 破解 签名校验

- com.shuqi.controller.apk
  - jadx 1.2.0 without jre 打开 com.shuqi.controller.apk, 只要一打开 AndroidManifest.xml 就会闪退, 使用 jadx-gui-1.2.0-with-jre-win 打开即可

- 搜索 signatures
  - com.shuqi.app.ShuqiApplication.checkSigAsync

```java

if (-1936262660 == context.getPackageManager().getPackageInfo(context.getPackageName(), 64).signatures[0].hashCode()) {
    return true;
}

```

- com.chaozhuo.texteditor.apk 签名校验

  - 使用 android killer 打开, 重新编译
  - 生成 com.chaozhuo.texteditor_killer.apk
  - 安装到手机中, 出现 非正版软件的提示
  - www.phoenixstudio.org

- 使用 jadx 打开 com.chaozhuo.texteditor.apk
  - 搜索 hashCode, 但没有找到如上面签名校验的代码, 需要进行 hook


- 编写 hook 脚本

```js

Java.perform(function () {
    console.log('enter hook')
    // https://developer.android.google.cn/reference/android/content/pm/Signature
    var Signature = Java.use('android.content.pm.Signature');
    // 会打印 'enter hook', 但是没有 'enter hashCode'
    // 说明 com.chaozhuo.texteditor.apk 不是使用的 hashCode 方法, 再尝试 Signature 的其它方法
    Signature.hashCode.implementation = function () {
        console.log('enter hashCode')
        // 调用 signature 本身的 hashCode 方法
        var hash = this.hashCode();
        console.log(hash);
        return hash;
    }

});

```

- 运行, 会打印 'enter hook', 但是没有 'enter hashCode', 说明 com.chaozhuo.texteditor.apk 不是使用的 hashCode 方法, 再尝试 Signature 的其它方法

- 在 https://developer.android.google.cn/reference/android/content/pm/Signature 查找与 hashCode 并行的其它方法, 如 toByteArray, 再次 hook

```js

Java.perform(function () {
    console.log('enter hook')
    // https://developer.android.google.cn/reference/android/content/pm/Signature
    var Signature = Java.use('android.content.pm.Signature');
    // 会打印 'enter hook', 但是没有 'enter hashCode'
    // 说明 com.chaozhuo.texteditor.apk 不是使用的 hashCode 方法, 再尝试 Signature 的其它方法
    Signature.hashCode.implementation = function () {
        console.log('enter hashCode')
        // 调用 signature 本身的 hashCode 方法
        var hash = this.hashCode();
        console.log(hash);
        return hash;
    }
    // 因为没有调用 hashCode 方法, 故尝试 toByteArray 方法
    Signature.toByteArray.implementation = function () {
        console.log('enter toByteArray')
        var byteArray = this.toByteArray();
        console.log(byteArray);
        return byteArray;
    }
});


```

- 再次运行 hook 脚本, 会打印出 'enter toByteArray', 详明调用了 toByteArray 方法

- 但在 com.chaozhuo.texteditor.apk 源码中没有搜索到 toByteArray 方法, 所以尝试通过打印 toByteArray 的调用栈来查看调用过程

```js

Java.perform(function () {
    console.log('enter hook')
    // https://developer.android.google.cn/reference/android/content/pm/Signature
    var Signature = Java.use('android.content.pm.Signature');
    // 会打印 'enter hook', 但是没有 'enter hashCode'
    // 说明 com.chaozhuo.texteditor.apk 不是使用的 hashCode 方法, 再尝试 Signature 的其它方法
    Signature.hashCode.implementation = function () {
        console.log('enter hashCode')
        // 调用 signature 本身的 hashCode 方法
        var hash = this.hashCode();
        console.log(hash);
        return hash;
    }
    // 因为没有调用 hashCode 方法, 故尝试 toByteArray 方法
    Signature.toByteArray.implementation = function () {
        console.log('enter toByteArray')
        printCallStack();
        var byteArray = this.toByteArray();
        console.log(byteArray);
        return byteArray;
    }
    // 因为 com.chaozhuo.texteditor.apk 源码中没有搜索到 toByteArray 方法
    // 打印 toByteArray 的调用栈
    function printCallStack() {
        console.log(Java.use("android.util.Log").getStackTraceString(Java.use("java.lang.Exception").$new()));
    }

});


```

- 打印出来的调用栈

```

java.lang.Exception
  at android.content.pm.Signature.toByteArray(Native Method)
  at java.lang.reflect.Method.invoke(Native Method)
  at com.chaozhuo.texteditor.widget.a.a(ARHelper.java:203)
  at com.chaozhuo.texteditor.widget.a.a(ARHelper.java:159)
  at com.chaozhuo.texteditor.widget.b.doInBackground(ARHelper.java:68)
  at android.os.AsyncTask$2.call(AsyncTask.java:305)
  at java.util.concurrent.FutureTask.run(FutureTask.java:237)
  at android.os.AsyncTask$SerialExecutor$1.run(AsyncTask.java:243)
  at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1133)
  at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:607)
  at java.lang.Thread.run(Thread.java:761)

```

- 以上调用栈中, 排除系统的方法, 只查找 texteditor app 的方法 com.chaozhuo.texteditor.widget.a.a
- 在 jadx 中找到 com.chaozhuo.texteditor.widget.a.a 方法

```java

public final class a {
    static boolean a(Context context) {
        String sb;
        try {
            byte[] a2 = a((byte[]) a(((Object[]) b(a(a(context, a("Z2V0UGFja2FnZU1hbmFnZXI=")), a("Z2V0UGFja2FnZUluZm8="), (String) a(context, a("Z2V0UGFja2FnZU5hbWU=")), String.class, 64, Integer.TYPE), a("c2lnbmF0dXJlcw==")))[0], a("dG9CeXRlQXJyYXk=")));
            if (a2 == null) {
                sb = null;
            } else {
                StringBuilder sb2 = new StringBuilder(a2.length * 2);
                for (byte b2 : a2) {
                    sb2.append("0123456789abcdef".charAt((b2 >> 4) & 15));
                    sb2.append("0123456789abcdef".charAt(b2 & 15));
                }
                sb = sb2.toString();
            }
            return sb.equals(a("YmUwZGQyYmM4ZmEzYjUzNDI0MWY5MWY4YjY2ZDFjNTA=")) || sb.equals(a("YzY1ZmUxNmFlOWYyNDkzZDdhNTA2MWEwMTk3MTVkMWY="));
        } catch (Exception e) {
            return true;
        }
    }

...

```

- base64 解码此代码中的字符串就可以得到 toByteArray 方法

- byte[] a2 = a((byte[]) a(((Object[]) b(a(a(context, a("Z2V0UGFja2FnZU1hbmFnZXI=")), a("Z2V0UGFja2FnZUluZm8="), (String) a(context, a("Z2V0UGFja2FnZU5hbWU=")), String.class, 64, Integer.TYPE), a("c2lnbmF0dXJlcw==")))[0], a("dG9CeXRlQXJyYXk=")))
- 查看 a("Z2V0UGFja2FnZU1hbmFnZXI=") 方法, 对字符串进行 base64 解码
- byte[] a2 = a((byte[]) a(((Object[]) b(a(a(context, "getPackageManager"), "getPackageInfo", (String) a(context, "getPackageName"), String.class, 64, Integer.TYPE), "signatures"))[0], "toByteArray"))
- 查看 a(context, "getPackageManager") 方法, 通过反射调用 从 context 中获取 getPackageManager, getPackageName 方法
- byte[] a2 = a((byte[]) a(((Object[]) b(a(context.getPackageManager(), "getPackageInfo", (String) context.getPackageName(), String.class, 64, Integer.TYPE), "signatures"))[0], "toByteArray"))

- byte[] a2 = a((byte[]) a(((Object[]) b(context.getPackageManager().getPackageInfo(context.getPackageName(), 64), "signatures"))[0], "toByteArray"))

- byte[] a2 = a((byte[]) a(context.getPackageManager().getPackageInfo(context.getPackageName(), 64).signatures)[0], "toByteArray"))

- byte[] a2 = a(context.getPackageManager().getPackageInfo(context.getPackageName(), 64).signatures)[0].toByteArray())

- 此方法就是 app 启动时签名检验的方法, 再次进行 hook, 且直接返回 True, 查看能否正常进入 app

```js

Java.perform(function () {
    console.log('enter hook')
    // https://developer.android.google.cn/reference/android/content/pm/Signature
    var Signature = Java.use('android.content.pm.Signature');
    // 会打印 'enter hook', 但是没有 'enter hashCode'
    // 说明 com.chaozhuo.texteditor.apk 不是使用的 hashCode 方法, 再尝试 Signature 的其它方法
    Signature.hashCode.implementation = function () {
        console.log('enter hashCode')
        // 调用 signature 本身的 hashCode 方法
        var hash = this.hashCode();
        console.log(hash);
        return hash;
    }
    // 因为没有调用 hashCode 方法, 故尝试 toByteArray 方法
    Signature.toByteArray.implementation = function () {
        console.log('enter toByteArray')
        // printCallStack();
        var byteArray = this.toByteArray();
        console.log(byteArray);
        return byteArray;
    }
    // 因为 com.chaozhuo.texteditor.apk 源码中没有搜索到 toByteArray 方法
    // 打印 toByteArray 的调用栈
    function printCallStack() {
        console.log(Java.use("android.util.Log").getStackTraceString(Java.use("java.lang.Exception").$new()));
    }

    var AA = Java.use('com.chaozhuo.texteditor.widget.a');
    // {'type': 'error', 'description': "Error: a(): has more than one overload, use .overload(<signature>) to choose from:\n\t.overload('java.lang.String')\n\t.overload('android.content.Context')\n\t.overload('[B')\n\t.overload('java.lang.Object', 'java.lang.String')\n\t.overload('java.lang.String', '[Ljava.lang.Class;', '[Ljava.lang.Object;')\n\t.overload('java.lang.Object', 'java.lang.String', 'java.lang.Object', 'java.lang.Class')\n\t.overload('java.lang.Object', 'java.lang.String', 'java.lang.Object', 'java.lang.Class', 'java.lang.Object', 'java.lang.Class')", 'stack': "Error: a(): has more than one overload, use .overload(<signature>) to choose from:\n\t.overload('java.lang.String')\n\t.overload('android.content.Context')\n\t.overload('[B')\n\t.overload('java.lang.Object', 'java.lang.String')\n\t.overload('java.lang.String', '[Ljava.lang.Class;', '[Ljava.lang.Object;')\n\t.overload('java.lang.Object', 'java.lang.String', 'java.lang.Object', 'java.lang.Class')\n\t.overload('java.lang.Object', 'java.lang.String', 'java.lang.Object', 'java.lang.Class', 'java.lang.Object', 'java.lang.Class')\n    at X (frida/node_modules/frida-java-bridge/lib/class-factory.js:563)\n    at K (frida/node_modules/frida-java-bridge/lib/class-factory.js:558)\n    at set (frida/node_modules/frida-java-bridge/lib/class-factory.js:925)\n    at <anonymous> (/script1.js:28)\n    at <anonymous> (frida/node_modules/frida-java-bridge/lib/vm.js:16)\n    at _performPendingVmOps (frida/node_modules/frida-java-bridge/index.js:238)\n    at <anonymous> (frida/node_modules/frida-java-bridge/index.js:230)\n    at apply (native)\n    at ne (frida/node_modules/frida-java-bridge/lib/class-factory.js:613)\n    at <anonymous> (frida/node_modules/frida-java-bridge/lib/class-factory.js:592)", 'fileName': 'frida/node_modules/frida-java-bridge/lib/class-factory.js', 'lineNumber': 563, 'columnNumber': 1}
    // 因为 AA.a 方法有很多个重载的方法, 程序不知道调用哪个, 就会报错
    // AA.a.implementation = function () {}
    // AA.a 的源码是 static boolean a(Context context) {}, 所以要写成如下的形式
    AA.a.overload('android.content.Context').implementation = function () {
        return true;
        // 因为 AA.a 方法的作用只是签名校验, 直接返回 true, 就不再需要再调用原来的 a 方法了
        // this.a();
    }

});


```

- 如何通过修改代码让程序通过启动时的签名校验, 只需要修改 com.chaozhuo.texteditor.widget.a.a 方法, 让它直接返回 true

- 修改 对应的 smali 代码
  - 通过 android killer 打开 com.chaozhuo.texteditor
  - 在 工程管理器 标签中找到 com.chaozhuo.texteditor.widget.a.smali
  - 搜索 Context, 找到 a 方法 `.method static a(Landroid/content/Context;)`
  - 可以删除此方法的所有代码, 只返回 true 即可


a.smali

```smali

.method static a(Landroid/content/Context;)Z

    // 使用 2 个寄存器
    .registers 2
    // 定义变量, 赋值为 true
    const/4 v1, 0x1
    return v1

.end method

```

- 再次编译为 apk 文件, 安装即可
- 除了 hook getByteArray 方法外, 还可以尝试 hook getPackageInfo, getPackageName 方法


#### 额外查阅

  - java.lang.Class: class类
    - https://www.apiref.com/android-zh/java/lang/Class.html

  - 安卓开发手册 API
    - https://developer.android.google.cn/reference/android/content/pm/Signature

  - 安卓开发手册 API (中文) 
    - https://www.matools.com/api/android

  - Java API
    - https://tool.oschina.net/apidocs/apidoc?api=jdk-zh


#### 演示 Class 类, 条件反射, PackageManager 类的使用

- 不同手段定位 Java 层 Signature 签名校验位置

  - 知识结构与经验决定了 hook 点位和代码定位方式
  - 我们学习目的就是掌握代码定位技术与代码分析

- 注意: java 和 python 的 bytes 结果有区别 补码问题

## 公共基础

- 安卓 7 以上安装抓包软件证书需要导入到证书系统目录, 才能被信任

  - openssl for windows
    - https://slproweb.com/products/Win32OpenSSL.html

  - openssl in git
    - C:\Program Files\Git\usr\bin\openssl.exe

  - 1.导出抓包软件pem证书
    - 如果导出cer证书要转换证书为pem格式
    - fiddler > Tools > Options > HTTPS > Actions > Export Root Certificate to Desktop > D:\David\Desktop\FiddlerRoot.cer
    - openssl x509 -inform der -in xxx.cer -out xxx.pem
    - openssl x509 -inform der -in FiddlerRoot.cer -out FiddlerRoot.pem

  - 2.输出证书内容
    - openssl x509 -inform PEM -subject_hash_old -in xxx.pem

  - FiddlerRoot.pem
    - openssl x509 -inform PEM -subject_hash_old -in D:\David\Desktop\FiddlerRoot.pem

```
269953fb
-----BEGIN CERTIFICATE-----
MIIDsjCCApqgAwIBAgIQdvl+9cPh+opDjL9ZduHbNzANBgkqhkiG9w0BAQsFADBn
MSswKQYDVQQLDCJDcmVhdGVkIGJ5IGh0dHA6Ly93d3cuZmlkZGxlcjIuY29tMRUw
EwYDVQQKDAxET19OT1RfVFJVU1QxITAfBgNVBAMMGERPX05PVF9UUlVTVF9GaWRk
bGVyUm9vdDAeFw0yMDA5MjIxMTE3NDFaFw0yMzEyMjIxMTE3NDFaMGcxKzApBgNV
BAsMIkNyZWF0ZWQgYnkgaHR0cDovL3d3dy5maWRkbGVyMi5jb20xFTATBgNVBAoM
DERPX05PVF9UUlVTVDEhMB8GA1UEAwwYRE9fTk9UX1RSVVNUX0ZpZGRsZXJSb290
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAqFs0CcehA5jdaPuG6tJz
qXY4IBTqVAknf2fSi8Ej8I7ezvW+IOeOGV48dtpm4mvRLqCiOQ7/V9Cog7bpVnca
1mHfDNvdiKj33keKJq6Js+L94qUlmVwA9oVGI8QfnCdgj2Ucb5kk7DYA77Ha0UIg
am53fCmRTFCUbFAn3vxDcRSMVLlaO05z6JaC8Co1VBb8bRbwVeKajgqfFm/cykUN
vd2GgrCLNZjF74JvwcfxCJVZX7xRk/o1tAX76dla0HSzkTLeSJ5ddT2RhDTkqmv7
NjVpCU+Q+e+9gGJy5iqIwG8D6+Y5gcILSrFT73I/4vBe8YATllrsen4nW6498S6d
/QIDAQABo1owWDATBgNVHSUEDDAKBggrBgEFBQcDATASBgNVHRMBAf8ECDAGAQH/
AgEAMB0GA1UdDgQWBBRLmrFs0jHgNrM/hJa7IPcuMYKQhjAOBgNVHQ8BAf8EBAMC
AQYwDQYJKoZIhvcNAQELBQADggEBADY0QrebxFQumz6id0fdzsh+m27Y2awHX2Ot
tbo2wSSa6R3eh484xpnRdiP3+hJkdu7txPZkEETvE+4dJUMRUYeNB+pE7q5pouFv
uHzKybOLiPwm3uYaGUHbHIIkeN4TK4Yjg9b1Di0YqymRMv+7d3I8/Boc8O2n/qIL
JRHTW7VoJyWyXnJbpEFR3/Vd/yfw38nGywFOW1Vc+D+rzU9z8jFKJcqPujisoJuN
iE48/vlWyEsXviPuzhRbse3B5lyXnYIexPItw/oi7zrE83hr4HXqybF+3tFWo96x
QenzLc3JsNh/oTT3mHOeyoWNfrR3k2d/QJE+4Q4/1aC1n+d+p0M=
-----END CERTIFICATE-----

```

  - 4.证书改名 (第三步输出的第一行为证书名), 证书文件名后加一个 .0
    - cp FiddlerRoot.pem 269953fb.0

  - 5.adb push 证书到手机 /sdcard/
    - adb push D:\David\Desktop\269953fb.0 /sdcard

  - 7.mv 证书到 /system/etc/security/cacerts/ 
    - cp /sdcard/269953fb.0 /system/etc/security/cacerts

    - 如果遇到 Read-only file system 
    - 执行命令:  mount -o rw,remount /system 重新挂在/system目录为可读写
    - 有些手机无法解决 Read-only file system

  - 6.给证书添加权限, su到root用户, 执行命令
    - 更改用户组: chgrp root 证书名
    - 更改用户组: chgrp root:root 269953fb.0
    - 更改文件权限: chmod 777 证书名 
    - 更改文件权限: chmod 777 269953fb.0 或者 chmod 644 269953fb.0 

  - 8.重启手机
    - 旧版本fiddler生成的证书在手机上可能有问题, 用最新版的fiddler
    - 下载并安装证书生成插件 https://www.telerik.com/fiddler/add-ons

  - .pem证书转.cer证书
    - openssl x509 -outform der -in demo.pem -out demo.cer

  - .cer证书转.pem证书
    - openssl x509 -inform der -in demo.cer -out demo.pem


使用 magisk 第三方模块安装证书到系统目录中

magisk > 模块 > Move Certificates 

会把安装到用户目录下的证书移动到系统目录中


### 破解 cookie 生成逻辑

- 安装 com.iCitySuzhou_nocheck_nopack.apk

- fiddler 抓包

- 点击一个专题


```

- request

GET https://app.suzhou-news.cn/topiclist?topicID=10000648 HTTP/1.1
Host: app.suzhou-news.cn
Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Linux; Android 7.1.2; MI 9 Build/NMF26X; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/92.0.4515.131 Mobile Safari/537.36 szxw
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
X-Requested-With: com.iCitySuzhou.suzhou001
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: szxw-uid=IMEI863254899701816-IMSI460071889901816; address=; longitude=4.9E-324; latitude=4.9E-324; sys=Android; sys_version=7.1.2; app_version=8.2; app_version_code=54; udid=IMEI863254899701816-IMSI460071889901816; timestamp=1637050586; signature=60f786e5ac2ac44ff298242958e4e535; uuid=; expires=1637050586907600000


- response

HTTP/1.1 200 OK
Date: Tue, 16 Nov 2021 08:16:27 GMT
Content-Type: text/html; charset=utf-8
Connection: keep-alive
Set-Cookie: acw_tc=2f624a5716370505872408638e428348ea9873c4cfe282d6eb01b9526c5b80;path=/;HttpOnly;Max-Age=1800
Vary: Accept-Encoding
Status: 200 OK
Cache-Control: must-revalidate, private, max-age=0
X-Request-Id: ee61ea74ae24b940cca18bb5a00db53c
X-UA-Compatible: IE=Edge,chrome=1
X-Runtime: 0.233795
X-Rack-Cache: miss
Set-Cookie: _gravity_broadcast_session=BAh7B0kiD3Nlc3Npb25faWQGOgZFVEkiJWI4ZjNmN2E0ODY4NmRjMDkzZTQ2YjI4NDlhNzNlYjA1BjsAVEkiEF9jc3JmX3Rva2VuBjsARkkiMTY2YzlVcGJEVGVpaU91aU9pbVhZNEE5Zm5uRnpTb1Y4bkw0KzFMUDkxTkU9BjsARg%3D%3D--6680947f1bb56c29be55f264a721c2c2f1570c59; path=/; HttpOnly
X-Powered-By: Phusion Passenger 6.0.6
X-Frame-Options: SAMEORIGIN
Set-Cookie: SERVERID=314e31658b5f0809d6bc808ca39fe1e8|1637050587|1637050587;Path=/
Strict-Transport-Security: max-age=86400
Content-Length: 53468


```

- jadx 中搜索 "signature

- com.iCitySuzhou.suzhou001.d.d

```
aa a3 = a2.e().b("sys", "Android").b("sysVersion", Build.VERSION.RELEASE).b("appVersion", MyApplication.getInstance().getAppVersion()).b("appVersionCode", String.valueOf(MyApplication.getInstance().getAppVersionCode())).b("udid", udid).b("clientType", "android").b("timestamp", valueOf).b("signature", a(udid, valueOf)).a();

```

- hook 此方法确认是否在此生成了 cookie 中的参数

```js

Java.perform(function () {
    console.log('enter hook')
    var DD = Java.use('com.iCitySuzhou.suzhou001.d.d');
    DD.a.overload('a.u$a').implementation = function (p) {
        console.log('enter a')
        var result = this.a(p);
        console.log(result);
        return result;
    }
});


```

- signature 的生成
  - b("signature", a(udid, valueOf))

- a(udid, valueOf) 方法

```java

    public static String a(String str, String str2) {
        return com.hualong.framework.b.a.a(String.format("%s&&%s&&%s", str, str2, "f1190aca-d08e-4041-8666-29931cd89dde"));
    }


```

- com.hualong.framework.b.a.a 方法

```java

package com.hualong.framework.b;

import java.security.MessageDigest;

public class a {
    public static String a(String str) {
        if (str == null) {
            return null;
        }
        StringBuffer stringBuffer = new StringBuffer();
        try {
            MessageDigest instance = MessageDigest.getInstance("MD5");
            instance.update(str.getBytes());
            byte[] digest = instance.digest();
            for (byte b2 : digest) {
                stringBuffer.append(Integer.toString((b2 >>> 4) & 15, 16)).append(Integer.toString(b2 & 15, 16));
            }
        } catch (Exception e) {
        }
        return stringBuffer.toString();
    }
}

```

- 通过 hook 确认 com.hualong.framework.b.a.a 是否是生成 signature 的方法

```js

Java.perform(function () {
    console.log('enter hook')
    var DD = Java.use('com.iCitySuzhou.suzhou001.d.d');
    DD.a.overload('a.u$a').implementation = function (p) {
        console.log('enter suzhou001.d.d.a')
        var result = this.a(p);
        console.log(result);
        return result;
    }

    var AA = Java.use('com.hualong.framework.b.a');
    AA.a.implementation = function (p) {
        console.log('enter framework.b.a.a')
        console.log('param', p);
        var sig = this.a(p);
        console.log('sig', sig);
        return sig;
    }

});


```

- 通过打印的 sig 值与 抓包的请求进行对比, 确认 com.hualong.framework.b.a.a 是否是生成 signature 的方法

```

param IMEI863254899701816-IMSI460071889901816&&1637052114&&f1190aca-d08e-4041-8666-29931cd89dde
sig 686de1bba0e8847f5fb3c8b343e9b3ca

```

- 使用 js 实现 com.hualong.framework.b.a.a 方法, 使用 param 得到 sig

```js


Java.perform(function () {
    console.log('enter hook')
    var DD = Java.use('com.iCitySuzhou.suzhou001.d.d');
    DD.a.overload('a.u$a').implementation = function (p) {
        console.log('enter suzhou001.d.d.a')
        var result = this.a(p);
        console.log(result);
        return result;
    }

    var AA = Java.use('com.hualong.framework.b.a');
    // AA.a.implementation = function (p) {
    //     console.log('enter framework.b.a.a')
    //     console.log('param', p);
    //     var sig = this.a(p);
    //     console.log('sig', sig);
    //     return sig;
    // }

    // https://tool.oschina.net/apidocs/apidoc?api=jdk-zh
    // 把字符串转化为 java 的 字符串
    var str = Java.use('java.lang.String').$new('IMEI863254899701816-IMSI460071889901816&&1637052114&&f1190aca-d08e-4041-8666-29931cd89dde')

    var stringBuffer = Java.use('java.lang.StringBuffer').$new()
    var instance = Java.use('java.security.MessageDigest').getInstance("MD5")
    instance.update(str.getBytes())
    var digest = instance.digest()

    var Integer = Java.use('java.lang.Integer')

    for (let i = 0; i < digest.length; i++) {
        stringBuffer.append(Integer.toString((digest[i] >>> 4) & 15, 16)).append(Integer.toString(digest[i] & 15, 16))
    }
    console.log('param', str)
    console.log('sig', stringBuffer.toString())

});

```

- 使用 python 生成 signature 代码

```py

# -*- coding: utf-8 -*-
import hashlib

uuid = 'IMEI863254899701816-IMSI460071889901816&&1637052114&&f1190aca-d08e-4041-8666-29931cd89dde'


def get1():

    digest = hashlib.md5(uuid.encode('utf-8')).digest()

    signature = []

    for i in digest:
        signature.append(hex((i >> 4) & 15).replace('0x', ''))
        signature.append(hex(i & 15).replace('0x', ''))

    print(''.join(signature))


def get2():
    instance = hashlib.md5()
    instance.update(bytearray(uuid.encode('utf-8')))
    digest = instance.digest()

    signature = []

    for i in digest:
        signature.append(format((i >> 4) & 15, 'x'))
        signature.append(format(i & 15, 'x'))

    print(''.join(signature))


get1()
get2()


```


- String udid = MyApplication.getInstance().getUDID()

- com.hualong.framework.LibApplication.getUDID

```java

import android.telephony.TelephonyManager;

public class LibApplication extends Application {


    public String getUDID() {
        String str;
        if (f.a(this.f3577b)) {
            String imei = getIMEI();
            String imsi = getIMSI();
            if (imei == null) {
                str = "NNNNNNNNNNNNNNN";
            } else {
                str = imei;
            }
            if (imsi == null) {
                imsi = "460NNNNNNNNNNNN";
            } else if (imsi.length() < 15) {
                for (int i = 0; i < 15 - imsi.length(); i++) {
                    imsi = imsi + "N";
                }
            }
            this.f3577b = ("IMEI" + str) + "-" + ("IMSI" + imsi);
        }
        return this.f3577b;
    }

    public final String getIMEI() {
        return ((TelephonyManager) getSystemService("phone")).getDeviceId();
    }

    public final String getIMSI() {
        return ((TelephonyManager) getSystemService("phone")).getSubscriberId();
    }

}

```

## 还原登录过程的 RSA 加密

- lesson5-com.picovr.assistantphone.apk PicoVR助手

- 使用手机号登录并抓包

```

- request

POST http://api.passport.picovr.com/loginUser HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 318
Host: api.passport.picovr.com
Connection: Keep-Alive
Accept-Encoding: gzip
User-Agent: okhttp/3.11.0

phone=18621013938&timestamp=1637113511299&enc=lcfqztAYz6Xy6zhhM84cmMdm8a42DRkk0M07R1f2g5Te%2F5eZPDOV2hmVslVYK%2F1hmBG41aRPtS910uklHwgkbP7elxUZlg%2BeIBsG4bUmGV6KvLy4w%2BMliWMSETqizw9X4Wkkp25l8zUVhuS%2BhNWkRbPPC2ZxT6xdr2pjT2dB2gc%3D&apiaccount=vrpuc-aaf91f835147ce2d01216bd3bd5c3516&sign=457878F51B0D47E0F5A176B976CD27E8

- response

HTTP/1.1 200
Server: nginx/1.18.0
Date: Wed, 17 Nov 2021 01:45:11 GMT
Content-Type: application/json;charset=UTF-8
Content-Length: 482
Connection: keep-alive
Set-Cookie: JSESSIONID=58CC0E9635EB24611EE4292A4C291DB2; Path=/; HttpOnly

{"data":{"refresh_token":"R/kAZr5wCtE7hQ/gP24p2LVWbBgWe4NRF+Rkxut2VGDvZYH9fDXNl6dRIbx/Z3B8o2kbgLN0Izg+S/X1iW+C6IrYVOlCCnWSnvum2BKi9UNC6xj1qGDzBeIxNz8Q0eE8gak3JhKTiKTuTwrcgN4bYQ31f425rxum4vf/y45KiKQ=","user_token_expire_time":14400,"sign":"634B5696C7843C6CEEDB6A6C630BEEE6","user_fresh_token_expire_time":1209600,"token":"TGT-1630283-3feotyMxwCmuayLCB9QhpMHn7i2EvsbOhfvMTKrCeDxknkeZhx-https://passport.picovr.com","timestamp":"1637113511398"},"ret_msg":"成功","ret_code":"success"}

```

- 破解请求参数

phone 18621013938
timestamp 1637113511299
enc lcfqztAYz6Xy6zhhM84cmMdm8a42DRkk0M07R1f2g5Te/5eZPDOV2hmVslVYK/1hmBG41aRPtS910uklHwgkbP7elxUZlg+eIBsG4bUmGV6KvLy4w+MliWMSETqizw9X4Wkkp25l8zUVhuS+hNWkRbPPC2ZxT6xdr2pjT2dB2gc=
apiaccount  vrpuc-aaf91f835147ce2d01216bd3bd5c3516
sign  457878F51B0D47E0F5A176B976CD27E8

- 使用 jadx 打开 com.picovr.assistantphone

- 搜索关键词 sign, "sign, "sign", 得到结果过多, 可以搜索 "enc"

- 过滤掉 请求参数中不存在的变量, 得到以下类和方法

com.picovr.assistantphone.c.a.e

```java

    public static O d(String str, String str2) {
        HashMap hashMap = new HashMap();
        hashMap.put("phone", str);
        hashMap.put("enc", e(str2));
        return b.c(f.h(), a(hashMap));
    }


```

- frida hook 确认是否是此方法生成的 enc 参数


```js


Java.perform(function () {
    console.log('enter hook')
    var EE = Java.use('com.picovr.assistantphone.c.a.e');
    EE.d.overload('java.lang.String', 'java.lang.String').implementation = function (p1, p2) {
        console.log('enter com.picovr.assistantphone.c.a.e')
        console.log('str', p1);
        console.log('str2', p2);
        var result = this.d(p1, p2);
        console.log(result);
        return result;
    }

});

```

- 进入到了此方法中, 打印的 str 和 str2 分别是 手机号和密码

- hashMap.put("enc", e(str2))

```java

    private static String e(String str) {
        String a2 = a(10);
        return d("password=" + str + "&salt=" + a2);
    }

```

- String a2 = a(10)

```java

    private static String a(int i2) {
        SecureRandom secureRandom = new SecureRandom();
        StringBuffer stringBuffer = new StringBuffer();
        for (int i3 = 0; i3 < i2; i3++) {
            stringBuffer.append(secureRandom.nextInt(10));
        }
        return stringBuffer.toString();
    }

```

- 创建 java 项目, 测试以上方法的作用
  - project yuanrenxue
  - package: com.yuanrenxue.Android
  - class: S2L5Login

```java

package com.yuanrenxue.Android;

import java.security.SecureRandom;

public class S2L5Login {
    public static void main(String[] args) {
        String salt = a(10);
        System.out.println(salt);
    }
    private static String a(int i2) {
        SecureRandom secureRandom = new SecureRandom();
        StringBuffer stringBuffer = new StringBuffer();
        for (int i3 = 0; i3 < i2; i3++) {
            stringBuffer.append(secureRandom.nextInt(10));
        }
        return stringBuffer.toString();
    }
}


```

- python 实现

```python

# -*- coding: utf-8 -*-
import random


class S2L5Login(object):
    phone = '18621013938'
    passwd = 'Xzz@8481'

    def get_salt(self, i2):
        """
        com.picovr.assistantphone.c.a.e

        import java.security.SecureRandom;

        private static String a(int i2) {
            SecureRandom secureRandom = new SecureRandom();
            StringBuffer stringBuffer = new StringBuffer();
            for (int i3 = 0; i3 < i2; i3++) {
                stringBuffer.append(secureRandom.nextInt(10));
            }
            return stringBuffer.toString();
        }
        """
        s = []
        for i in range(10):
            s.append(str(random.randint(0, 10)))
        return ''.join(s)

    def run(self):
        salt = self.get_salt(i2=10)
        print(salt)


if __name__ == '__main__':
    s2l5_login = S2L5Login()
    s2l5_login.run()


```

- 或者在 hook 代码中直接调用 a 函数, 查看返回值

```js


Java.perform(function () {
    console.log('enter hook')
    var EE = Java.use('com.picovr.assistantphone.c.a.e');
    EE.d.overload('java.lang.String', 'java.lang.String').implementation = function (p1, p2) {
        console.log('enter com.picovr.assistantphone.c.a.e')
        console.log('str', p1);
        console.log('str2', p2);
        var result = this.d(p1, p2);
        console.log(result);
        return result;
    }
    
    // 主动调用 a 方法, 不需要进入到方法中
    // 因为 a 是 static 方法, 所以可以直接使用类来调用
    var salt = EE.a(10);
    console.log('salt', salt)

});

```

- 或者直接在 hook 代码中实现 a 函数

```js


Java.perform(function () {
    console.log('enter hook')
    var EE = Java.use('com.picovr.assistantphone.c.a.e');
    EE.d.overload('java.lang.String', 'java.lang.String').implementation = function (p1, p2) {
        console.log('enter com.picovr.assistantphone.c.a.e')
        console.log('str', p1);
        console.log('str2', p2);
        var result = this.d(p1, p2);
        console.log(result);
        return result;
    }

    // 主动调用 a 方法, 不需要进入到方法中
    // 因为 a 是 static 方法, 所以可以直接使用类来调用
    // var salt = EE.a(10);
    // console.log('salt', salt)

    // 实现 a 方法
    function getSalt(i2) {

        var secureRandom = Java.use('java.security.SecureRandom').$new();
        var stringBuffer = Java.use('java.lang.StringBuffer').$new();
        for (var i3 = 0; i3 < i2; i3++) {
            var randInt = secureRandom.nextInt(10)
            console.log(i3+1, randInt)
            stringBuffer.append(randInt);
        }
        console.log('salt', stringBuffer)

    }

    getSalt(10);

});

```

- d("password=" + str + "&salt=" + a2)

```java


    private static String d(String str) {
        try {
            return a.a(d.a(str.getBytes(), d.b(b.p)));
        } catch (Exception unused) {
            return "";
        }
    }


```

- b.p
  - com.picovr.assistantphone.c.a.b
  - public static final String p = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDDZ2bnVXHfDEJnTic8//BK3Bkm\r2DnraCvnknXdqd+84ZYtRhYA/+MsQWv1k6qH6aR1yXagtnGVZeoO3722fWDtIQFK\regY2LZuADRONNNJC75+WTaHuQfXULXynMGwkkhtXCt0HrGtOVpJ1jP60HrJ4qVYa\rlM+8svZbYNYLqfOsHwIDAQAB";

- d.b
  - com.picovr.assistantphone.c.a.d


```java

    public static PublicKey b(String str) throws Exception {
        try {
            return KeyFactory.getInstance(f16050a).generatePublic(new X509EncodedKeySpec(a.a(str)));
        } catch (NoSuchAlgorithmException unused) {
            throw new Exception("无此算法");
        } catch (InvalidKeySpecException unused2) {
            throw new Exception("公钥非法");
        } catch (NullPointerException unused3) {
            throw new Exception("公钥数据为空");
        }
    }


```

- new X509EncodedKeySpec(a.a(str)) 中的 a.a(str)


```java

    public static byte[] a(String str) {
        try {
            return b(str);
        } catch (UnsupportedEncodingException e2) {
            e2.printStackTrace();
            return new byte[0];
        }
    }


```

- b(str)

```java


    /* JADX WARNING: Removed duplicated region for block: B:13:0x0037  */
    /* JADX WARNING: Removed duplicated region for block: B:23:0x0061  */
    /* JADX WARNING: Removed duplicated region for block: B:33:0x008b A[LOOP:0: B:1:0x000d->B:33:0x008b, LOOP_END] */
    /* JADX WARNING: Removed duplicated region for block: B:36:0x0097 A[EDGE_INSN: B:36:0x0097->B:34:0x0097 ?: BREAK  , SYNTHETIC] */
    /* JADX WARNING: Removed duplicated region for block: B:39:0x0097 A[EDGE_INSN: B:39:0x0097->B:34:0x0097 ?: BREAK  , SYNTHETIC] */
    /* JADX WARNING: Removed duplicated region for block: B:40:0x0097 A[EDGE_INSN: B:40:0x0097->B:34:0x0097 ?: BREAK  , SYNTHETIC] */
    /* JADX WARNING: Removed duplicated region for block: B:42:0x0097 A[EDGE_INSN: B:42:0x0097->B:34:0x0097 ?: BREAK  , SYNTHETIC] */
    /* JADX WARNING: Removed duplicated region for block: B:8:0x0025 A[LOOP:2: B:8:0x0025->B:11:0x0032, LOOP_START, PHI: r5 
      PHI: (r5v1 int) = (r5v0 int), (r5v9 int) binds: [B:7:0x0021, B:11:0x0032] A[DONT_GENERATE, DONT_INLINE]] */
    /* Code decompiled incorrectly, please refer to instructions dump. */
    private static byte[] b(java.lang.String r9) throws java.io.UnsupportedEncodingException {
        /*
        // Method dump skipped, instructions count: 160
        */
        throw new UnsupportedOperationException("Method not decompiled: com.picovr.assistantphone.c.a.a.b(java.lang.String):byte[]");
    }


```

- JADX 编译错误, 文件 > 首选项 > 显示不一致的反编译 (Show inconsistent code)

- com.picovr.assistantphone.c.a.a, 可以使用 java 来测试, 这个类中的两个方法分别是 base64 的编码和解码

- hook 这个方法, 查看其作用, 或者直接调用, 查看结果

```js

Java.perform(function () {
    console.log('enter hook')
    var EE = Java.use('com.picovr.assistantphone.c.a.e');
    EE.d.overload('java.lang.String', 'java.lang.String').implementation = function (p1, p2) {
        console.log('enter com.picovr.assistantphone.c.a.e')
        console.log('str', p1);
        console.log('str2', p2);
        var result = this.d(p1, p2);
        console.log(result);
        return result;
    }

    // 主动调用 a 方法, 不需要进入到方法中
    // 因为 a 是 static 方法, 所以可以直接使用类来调用
    // var salt = EE.a(10);
    // console.log('salt', salt)

    // 实现 a 方法
    function getSalt(i2) {

        var secureRandom = Java.use('java.security.SecureRandom').$new();
        var stringBuffer = Java.use('java.lang.StringBuffer').$new();
        for (var i3 = 0; i3 < i2; i3++) {
            var randInt = secureRandom.nextInt(10)
            console.log(i3+1, randInt)
            stringBuffer.append(randInt);
        }
        console.log('salt', stringBuffer)

    }
    // getSalt(10);

    function getPkey() {
        var CC = Java.use('com.picovr.assistantphone.c.a.a');
        var stringBuffer = Java.use('java.lang.StringBuffer').$new("MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDDZ2bnVXHfDEJnTic8//BK3Bkm\r2DnraCvnknXdqd+84ZYtRhYA/+MsQWv1k6qH6aR1yXagtnGVZeoO3722fWDtIQFK\regY2LZuADRONNNJC75+WTaHuQfXULXynMGwkkhtXCt0HrGtOVpJ1jP60HrJ4qVYa\rlM+8svZbYNYLqfOsHwIDAQAB");
        var aaa = CC.b(stringBuffer.toString())
        console.log(JSON.stringify(aaa))
    }
    // 直接调用 com.picovr.assistantphone.c.a.a 方法
    getPkey();

    // hook com.picovr.assistantphone.c.a.a 方法
    var BB = Java.use('com.picovr.assistantphone.c.a.a');
    BB.b.implementation = function (p) {
        console.log('enter com.picovr.assistantphone.c.a.a')
        console.log('str', JSON.stringify(p));
        var result = this.b(p);
        console.log('result', JSON.stringify(result));
        return result;
    }

});
```

```

str "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDDZ2bnVXHfDEJnTic8//BK3Bkm\r2DnraCvnknXdqd+84ZYtRhYA/+MsQWv1k6qH6aR1yXagtnGVZeoO3722fWDtIQFK\regY2LZuADRONNNJC75+WTaHuQfXULXynMGwkkhtXCt0HrGtOVpJ1jP60HrJ4qVYa\rlM+8svZbYNYLqfOsHwIDAQAB"
result [48,-127,-97,48,13,6,9,42,-122,72,-122,-9,13,1,1,1,5,0,3,-127,-115,0,48,-127,-119,2,-127,-127,0,-61,103,102,-25,85,113,-33,12,66,103,78,39,60,-1,-16,74,-36,25,38,-40,57,-21,104,43,-25,-110,117,-35,-87,-33,-68,-31,-106,45,70,22,0,-1,-29,44,65,107,-11,-109,-86,-121,-23,-92,117,-55,118,-96,-74,113,-107,101,-22,14,-33,-67,-74,125,96,-19,33,1,74,122,6,54,45,-101,-128,13,19,-115,52,-46,66,-17,-97,-106,77,-95,-18,65,-11,-44,45,124,-89,48,108,36,-110,27,87,10,-35,7,-84,107,78,86,-110,117,-116,-2,-76,30,-78,120,-87,86,26,-108,-49,-68,-78,-10,91,96,-42,11,-87,-13,-84,31,2,3,1,0,1]

```

- 作用是把 bas64 str 转化为 bytes array, 使用 python 来实现

```python

    def get_pk(self, p):
        """
        使用 python base64 模块
        :param p:
        :return:
        """
        return base64.b64decode(p.encode('utf-8')).hex()

```

- 得到结果

  - 30819f300d06092a864886f70d010101050003818d0030818902818100c36766e75571df0c42674e273cfff04adc1926d839eb682be79275dda9dfbce1962d461600ffe32c416bf593aa87e9a475c976a0b6719565ea0edfbdb67d60ed21014a7a06362d9b800d138d34d242ef9f964da1ee41f5d42d7ca7306c24921b570add07ac6b4e5692758cfeb41eb278a9561a94cfbcb2f65b60d60ba9f3ac1f0203010001
  - 与 app 中的 array 的值是有符号和无符号的区别, 值其实是一样的, 如 0x30 & 0xFF > 48, hex(48 & 0xff) > 0x30, hex(-127 & 0xff) > 81


- d.b(b.p) 传入字符串, 生成公钥

```java


    public static PublicKey b(String str) throws Exception {
        try {
            return KeyFactory.getInstance(f16050a).generatePublic(new X509EncodedKeySpec(a.a(str)));
        } catch (NoSuchAlgorithmException unused) {
            throw new Exception("无此算法");
        } catch (InvalidKeySpecException unused2) {
            throw new Exception("公钥非法");
        } catch (NullPointerException unused3) {
            throw new Exception("公钥数据为空");
        }
    }


```

- python 实现

- python 加密库
  - cryptography
    - https://github.com/pyca/cryptography
    - pip install cryptography
  - pycryptodome
    - https://github.com/Legrandin/pycryptodome
    - pip install pycryptodome


```python



```

- d.a(str.getBytes(), d.b(b.p)) 传入公钥, 对密码进行加密

```java

    public static byte[] a(byte[] bArr, PublicKey publicKey) {
        try {
            Cipher instance = Cipher.getInstance("RSA");
            instance.init(1, publicKey);
            return instance.doFinal(bArr);
        } catch (Exception e2) {
            e2.printStackTrace();
            return null;
        }
    }


```


## RSA 加密

### 介绍

n = p1 × p2 一个正整数可以分解为两个互质数相乘, 本质是一个超大数运算
n 是密钥
公钥 = (n, e) 
私钥 = (n, d)

是非对称加密 (分公钥和私钥, 不是依靠同一个密码来加解密) , 依靠随机填充 (padding) 可以达到: 同一个明文, 同一个公钥每次生成不同的密文, 而私钥能正确解开密文. 
公钥加密, 私钥解密
私钥加密, 公钥解密
安全性足够高, 不可逆 (算力不足) 
公钥在客户端, 私钥在服务器 (私钥不能泄露) 

RSA密钥长度1024位, 2048, 3072....., 长度越长越安全, 运算速度越慢

### paddind(填充)

- 填充模式
  - NoPadding, OAEPPadding, PKCS1Padding等, PKCS#1的padding就占用了11个字
  节
  - 公钥和私钥使用不一样的填充方式会导致解密失败
  - NoPadding填充模式不安全

- 为什么需要填充 (padding) 
  - 算法特点决定了对要加密的数据有长度要求, 通常是16的整数倍, 不是16的倍数, 就需要填充到这么长, 如果刚好是16倍数, 也需要再填充. 

- 密文长度等于密钥长度
  - 明文最大长度=密钥长度-11 (单位字节) , 超过最大长度需要分块加密或者先把明文用对称加密方式加密缩短长度后再加密. 
  - 假设密钥为1024位 (密钥至少1024位才安全, 长度小于它被证明会被破解) , 即1024位/8位-11=128-11=117字节, 所以加密的明文最大长度是117字节, 解密密文最大长度为128字. 

- 为什么明文=密钥长度-11字节？
  - 因为RSA加密有填充模式 (padding) , 需要留11字节用来随机填充, 随机填充会达到同一个明文, 同一个公钥每次生成不同的密文. 

  - 1字节=8位 1024位/8=128字节
  - 公钥, 私钥是pem格式

  - android的rsa加密填充方式是RSA时, 是NoPadind RSA/ECB/NoPadding, 而标准jdk
  里填充是RSA时, 是指PKCS1填充, RSA/ECB/PKCS1Padding, 要注意

### RSA额外阅读

- RSA加密科普
  - https://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html 

- RSA加密科普
  - https://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html

- RSA密钥长度关系
  - https://cloud.tencent.com/developer/article/1199963

- python rsa加密库 
  - https://pycryptodome.readthedocs.io/en/latest/src/examples.html#generate-an-rsakey

- 公私钥ASN.1结构
  - https://blog.csdn.net/wzj_whut/article/details/86477568

- ASN.1, PKCS, PEM间的关系
  - https://blog.csdn.net/qq_39385118/article/details/107510032


## AES加密

- 一种对称加密, 加密和解密时需要: 密匙(key), iv, 加密模式三个参数, 加密时明文需要先
做对齐处理, 明文长度要为16的倍数, 否则要给明文后面加0补齐长度. 

- 递归查找文件里包含 "" 的文件

grep -n -ri "base_url" ./

frida hook callback abstract interface


## 刷机并使用 magisk root

### 工具准备

- 升级 nexus 6p 到安卓 8, 并 ROOT
- 工具准备
  - magisk
  - 安卓镜像
  - adb
  - fastboot
  - 手机电量50%以上

- 工具
  - 链接: https://pan.baidu.com/s/16LJ51c2NfFPlRBKlTl3YnA
  - 提取码: hfy6

- masgisk
  - https://github.com/topjohnwu/Magisk/releases


### 整体流程

#### 打开开发者模式, 允许 usb 调试, 不锁定屏幕

#### 升级安卓系统

- 下载安卓 8.1 镜像文件
- https://developers.google.cn/android/images?hl=zh-cn#shamu
- angler-opm7.181205.001-factory-b75ce068.zip
- 解压到 angler-opm7.181205.001
- `adb reboot bootloader` 进入 bootloader, 或关机状态下加下音量下 + 开机键
- `fastboot devices` 查看 fastboot 设备
- cmd 运行 angler-opm7.181205.001\flash-all.bat
- 安装完后, `fastboot reboot` 重启手机, 或点击电源键启动手机.

#### 打开开发者模式, 允许 usb 调试, 不锁定屏幕

#### 解 bootloader 锁

- 如果开发者选项中 `OEM 解锁` 或 `OEM unlocking` 功能已经打开, 表示已经解锁, 就跳到步骤 5 继续操作
- `adb devices` 查看设备
- adb reboot bootloader
- fastboot oem unlock
- 控制音量键选择 YES, 按电源键确认 YES, 等待手机解锁, 手机重启后, 解锁成功
- fastboot reboot 重启手机
- 确定开发者选项中 `OEM 解锁` 或 `OEM unlocking` 功能是否已经打开

#### 安装 magisk 并 root

- 下载 magisk 最新版
  - https://github.com/topjohnwu/Magisk/releases
- 安装 magisk 框架到手机
  - adb install Magisk-v23.0.apk
- 使用 magisk 破解安卓启动文件
  - 解压安卓里的镜像文件, angler-opm7.181205.001\image-angler-opm7.181205.001.zip, 得到 boot.img 文件
  - 把 boot.img 上传到手机
    - adb push boot.img /sdcard/Download/
  - 手机上使用 magisk, 生成 magisk_patched-23000_xxxxx.img 文件
    - Magisk > 安装 > 下一步 > 选择并修补一个文件 > /sdcard/download/boot.img > 开始 > 
    - 出现以下日志, 表示成功

```
...
- Patching ramdisk
- Repacking boot img
- Signing boot image with verity keys

********************************************
Output file is written to
/storage/emulated/0/Download/magisk_patched-23000_FVMR5.img
********************************************

- All done!
```

- root 手机
  - 把生成的 magisk_patched-23000_xxxxx.img 通过 adb pull 到电脑
    - adb pull /storage/emulated/0/Download/magisk_patched-23000_tfWDR.img
  - adb reboot bootloader 手机进入 bootloader
  - 执行命令 fastboot flash boot magisk_patched-23000_tfWDR.img 刷 magisk 破解的 img 文件
  - fastboot reboot 重启手机

- 确认手机已经 root
  - adb shell 进入手机系统
  - su 切换 root 用户
  - 如果出现了 超级权限的申请, 就表示 root 成功了

#### magisk 简单配置

- Settings > MagiskHide
- Settings > 请求超时 > 20s
- 屏蔽 app root 检测功能
  - 超级用户标签页 > MagiskHide > 选择 app
  - 安装中国工商银行 app, 尝试打开, 然后再添加到 MagiskHide 列表中, 再次尝试, 确认是否还能被检测到

#### 安装 xposed

- 下载 xposed installer
  - 科学上网, 直接在线安装
  - 从此页中下载 xposed-installer
    - https://github.com/rovo89/XposedInstaller
    - https://repo.xposed.info/module/de.robv.android.xposed.installer
    - https://forum.xda-developers.com/t/official-xposed-for-lollipop-marshmallow-nougat-oreo-v90-beta3-2018-01-29.3034811/
    - https://forum.xda-developers.com/attachments/xposedinstaller_3-1-5-apk.4393082/

  - 安装到手机 adb install XposedInstaller_3.1.5.apk
  - 打开 Xposed Installer, 安装 Xposed, 重启手机

- xposed 安装模块 GravityBox 重装盒子

  - 重启手机
  - 状态栏设置 > 电量设置


#### 8.1 以上版本安装 EdXposed
  - https://github.com/ElderDrivers/EdXposed

#### VirtualXposed
  - https://github.com/android-hacker/VirtualXposed


### 提高 android 逆向能力的方法

- 掌握基础语法, 掌握类/包的使用

- 了解 Java & 安卓 的一些类

  - PackageManager类
  - java.security.Signature
  - java.lang.Class
  - Context类
  - java.util.HashMap Map SortedMap TreeMap
  - javax.crypto.Cipher
  - java.security.PublicKey
  - android.util.Log
  - java.security.MessageDigest
  - javax.crypto.Mac
  - javax.crypto.spec.SecretKeySpec
  - javax.crypto.spec.DESKeySpec
  - javax.crypto.spec.IvParameterSpec
  - java.security.spec.X509EncodedKeySpec
  - java.security.spec.RSAPublicKeySpec
  - https://blog.csdn.net/LonelyRoamer/article/details/7656338


安卓API getSystemService : 获取指定服务对象
IMEI-->deviceid
getDeviceId
deviceid 额外阅读
https://www.jianshu.com/p/68839eaf7c72
https://blog.csdn.net/qq_35427437/article/details/80889120


### scrcpy 投屏

https://github.com/Genymobile/scrcpy


### 下载其它版本的 platform-tools

- android 版本市场占有率
https://www.statista.com/statistics/921152/mobile-android-version-share-worldwide/


https://developer.android.com/studio/releases/platform-tools

最新版
https://dl.google.com/android/repository/platform-tools-latest-windows.zip

31.0.3（2021 年 8 月）

https://dl.google.com/android/repository/platform-tools_r31.0.3-windows.zip

30.0.5（2020 年 11 月）
https://dl.google.com/android/repository/platform-tools_r30.0.5-windows.zip

29.0.6（2020 年 2 月）
https://dl.google.com/android/repository/platform-tools_r29.0.6-windows.zip

28.0.2（2019 年 3 月）
https://dl.google.com/android/repository/platform-tools_r28.0.2-windows.zip

27.0.1（2017 年 12 月）
https://dl.google.com/android/repository/platform-tools_r27.0.1-windows.zip

26.0.2（2017 年 10 月）
https://dl.google.com/android/repository/platform-tools_r26.0.2-windows.zip

25.0.5（2017 年 4 月 24 日）
https://dl.google.com/android/repository/platform-tools_r25.0.5-windows.zip


### 安卓版本与 api 对应关系

https://developer.android.com/studio/releases/platforms


Android 11 (API 级别 30)
Android 10（API 级别 29）
Android 9（API 级别 28）
Android 8.1（API 级别 27）
Android 8.0（API 级别 26）
Android 7.1（API 级别 25）
Android 7.0（API 级别 24）
Android 6.0（API 级别 23）
Android 5.1（API 级别 22）
Android 5.0（API 级别 21）
Android 4.4W（API 级别 20）
Android 4.4（API 级别 19）
Android 4.3（API 级别 18）
Android 4.2（API 级别 17）
Android 4.1（API 级别 16）
Android 4.0.3（API 级别 15）
Android 4.0（API 级别 14）
Android 3.2（API 级别 13）
Android 3.1（API 级别 12）
Android 3.0（API 级别 11）
Android 2.3.3（API 级别 10）
Android 2.3（API 级别 9）


### 开启开发者选项

打开 USB 调试
打开 USB 安装
不锁定屏幕


### jadx 

#### windows jadx 配置

- 下载: https://github.com/skylot/jadx/releases
  - jadx-1.2.0.zip
  - jadx-gui-1.2.0-with-jre-win.zip

- 解压到 C:/Develop/jadx

- 添加 Path 环境变量
  - C:\Develop\jadx
  - C:\Develop\jadx\bin

- jadx-1.2.0.zip 提供了命令行工具, 所以也要下载, 解压, 把 bin 目录添加到环境变量
  - windows 慎用, 每次都卡死

- jadx 常用命令
  - jadx --help
  - jadx -e xxx.apk
    - 把 apk 导出为安卓项目, 再使用 android-studio 打开
