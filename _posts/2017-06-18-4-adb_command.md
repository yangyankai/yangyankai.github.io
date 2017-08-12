---
layout: post
category : post
tagline: "Supporting tagline"
tags : [  beginner]
---
{% include JB/setup %}
 
 
 
### adb 选择链接指定devices
adb  -s NHTC170052 shell
 
 
### 关机命令
adb shell reboot -p
 
### 拷贝东西到电脑
adb pull xxx D:/ 不行的时候，再给D盘下指定个目录
adb pull data/user_de/0/com.android.shell/files/bugreports/bugreport-NZS26.71-19-2017-05-27-14-25-15.zip D:\Zuisys
adb pull system/framework/services.jar D:/
 
### 通过命令行执行adb shell am broadcast发送广播通知。
adb shell am broadcast 后面的参数有：
 
[-a <ACTION>]
[-d <DATA_URI>]
[-t <MIME_TYPE>]
[-c <CATEGORY> [-c <CATEGORY>] ...]
[-e|--es <EXTRA_KEY> <EXTRA_STRING_VALUE> ...]
[--ez <EXTRA_KEY> <EXTRA_BOOLEAN_VALUE> ...]
[-e|--ei <EXTRA_KEY> <EXTRA_INT_VALUE> ...]
[-n <COMPONENT>]
[-f <FLAGS>] [<URI>]
 
例如：
adb shell am broadcast -a com.Android.test --es test_string "this is test string" --ei test_int 100 --ez test_boolean true
 
 
adb 读写 setting
adb 读取 Global 设置中的值 adb shell settings get global charging_sounds 其中"charging_sounds"为键
adb 写入 System 设置中的值 adb shell settings put system screen_brightness
 
user版本，不能root。adb root 无效
 
### 查看进程
`ps | grep ^u0`
 
 
### 查看当前App
adb shell dumpsys window windows | grep -E 'mCurrentFocus|mFocusedApp'
 
 
### APP的安装
android 安装非系统apk
第一步，把apk放到手机上
adb push D:\yangyk3\StudioProjects\HelloAndroid\app\build\outputs\apk\app-debug.apk /data/local/tmp/com.example.yangyk3.helloandroid
第二步，安装手机上的apk
adb shell pm install -r "/data/local/tmp/com.example.yangyk3.helloandroid
 
### android安装系统apk
把路径换为 /system/app 即可
 
### 卸载apk
adb uninstall  "/data/local/tmp/com.example.yangyk3.helloandroid.apk"
adb uninstall com.example
 
### SystemUI
替换SystemUI， 原生的名字SystemUI.apk ， Zuk的名字为XuiSystemUI.apk.把原生的重命名为XuiSystemUI.apk。push到/system/priv-ap/XuiSystemUI/目录下
 
### ADB 命令
进入adb shell
adb shell
退出
exit
重启命令 
adb reroot
http://www.cnblogs.com/playing/archive/2010/09/19/1830799.html
http://blog.csdn.net/janronehoo/article/details/6863772/
 
### abd在手机里面找东西先root
 
### sqlite
进入sqlite
adb shell(进入shell)
sqlite3 （进入sqlite）
退出sqlite
.quit （退出sqlite，返回shell）
查看数据库：
sqlite3 demo.db  [Android   .open demo.dp]
查看表
.tables
Atable Btable
查看数据库
select * from Btable

### 查看日志
adb logcat -s 标签名
 
### 删除系统App
adb remount （重新挂载系统分区，使系统分区重新可写）。
      adb shell
      cd system/app
      rm *.apk
 
 
 
### 拷贝
cp  file  file_2
 
### 卸载 data/data 下的某个包名对应的apk
adb uninstall com.example.hello  //不需要到某个apk
 
### 替换Jar包
adb push D:\ZUI\z2x\services.jar  system/framework/services.jar
 
### 重命名 rename
例如: rename 1.txt 2.bat
文件1.txt 变为2.bat
 
### 关机
adb shell shutdown
 
### 查看手机版本信息
adb shell getprop
可查看手机系统的具体命令如下：
getprop 查看机器的全部信息参数
getprop ro.serialno 查看机器的SN号
getprop ro.carrier 查看机器的CID号
getprop ro.hardware 查看机器板子代号
 
adb shell getprop | grep build
[ro.build.args]: []
[ro.build.characteristics]: [phone]
[ro.build.cta]: [noncta]
[ro.build.date.utc]: [1495871049]
[ro.build.date]: [2017年 05月 27日 星期六 15:44:09 CST]
[ro.build.description]: [meizu_PRO5-user 5.1 LMY47D 1495870962 release-keys]
[ro.build.display.id]: [Flyme 6.1.0.0A]
[ro.build.fingerprint]: [Meizu/meizu_PRO5/PRO5:5.1/LMY47D/1495870962:user/release-keys]
[ro.build.flavor]: [meizu_m86-user]
[ro.build.host]: [Mz-Builder-l3]
[ro.build.id]: [LMY47D]
[ro.build.inside.id]: [5.1-20170527152857]
[ro.build.mask.id]: [5.1-1495870137_stable]
[ro.build.product]: [PRO5]
[ro.build.tags]: [release-keys]
[ro.build.type]: [user]
[ro.build.user]: [flyme]
[ro.build.version.all_codenames]: [REL]
[ro.build.version.codename]: [REL]
[ro.build.version.incremental]: [1495870962]
[ro.build.version.release]: [5.1]
[ro.build.version.sdk]: [22]
[ro.build.version.security_patch]: [2017-04-05]
[ro.meizu.build.spt]: [0] 
 
### 连接 USB 时不充电
adb shell dumpsys battery unplug
 
### 重置为充电状态
adb shell dumpsys battery reset
 
### 进入 IDLE
adb shell dumpsys deviceidle step
 
 
### adb 命令链接wifi
adb push D:/ZUIsys/wpa_supplicant.conf  /data/misc/wifi/   // 第一次手动连接wifi，之后把这个文件pull出来，刷机之后再把文件push进去
然后重启一下wifi
打开WIFI指令  
     svc wifi enable
关闭WIFI指令
    svc wifi disable
 
 
