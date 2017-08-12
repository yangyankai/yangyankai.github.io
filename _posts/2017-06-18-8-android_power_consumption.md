---

layout: post

category : post

tagline: "Supporting tagline"

tags : [battery , beginner]

---

{% include JB/setup %}


### 1 谷歌电量分析工具
通用的耗电信息 battery stats

Battery-Historian V1
数据源：         adb shell dumpsys batterystats > data_v1     【ZUK现在日志已包含】
分析工具：     执行Python脚本 historian.py
命令：              Python historian.py –a data_v1 > data_v1.html
运行：              用谷歌浏览打开 data_v1.html（需要VPN），就可以看到图形化界面
Battery-Historian V2
数据源：         adb bugreport data_v2.zip                                  【ZUK现在日志未包含】
分析工具：     使用Battery-Historian V2 Go 项目分析数据
命令：              go run cmd\battery-historian\battery-historian.go
运行：              用谷歌浏览打开http://localhost:9999/ （需要VPN），在界面中选择data_v2.zip


### 2 other
除了从android batterystats 中分析问题，各厂商可能会加入自己的一些feature，记录耗电和 CPU 实用信息等，可辅助分析异常耗电、发热等问题

