---

layout: post

category : post

tagline: "Supporting tagline"

tags : [battery , beginner]

---

{% include JB/setup %}




1 看是否存在耗电异常情况。
Android 耗电情况记录在 batterystats 文件中，使用 adb shell dumpsys batterystats 命令查看。主要看经过了多长时间，耗了多少电，有多少应用在后台运行，并分析这些运行情况。

standby for a night 
耗电

3%-5%	良

5%-8%	较好

8%-10%	较差


一晚上待机耗电超过 10% 以上不合格的，需要找出耗电多的原因并分析解决。



### 1 text analysic


我一般会看 batterystats 文件中  Estimated power use (mAh) 项，查看主要耗电的 uid，
```
Estimated power use (mAh):
  Capacity: 3500, Computed drain: 1987, actual drain: 3150-3185
  Uid 9999: 1239 ( cpu=1239 )
  Unaccounted: 1163 ( )
  Screen: 241
  Idle: 232
  Uid 0: 83.1 ( cpu=82.8 radio=0.314 wifi=0.00000209 )
  Uid u0a192: 53.8 ( cpu=53.7 wifi=0.0960 sensor=0.0103 )
  Uid 1000: 44.5 ( cpu=42.3 wake=0.716 radio=1.01 wifi=0.125 gps=0.254 sensor=0.0744 )
  Uid u0a169: 20.0 ( cpu=19.9 sensor=0.0224 )
  Uid u0a126: 16.8 ( cpu=16.6 wifi=0.197 sensor=0.00712 )
  Uid u0a39: 13.2 ( cpu=13.2 wake=0.00348 sensor=0.000364 )
  Wifi: 12.3 ( cpu=0.192 wifi=12.1 )
  Uid u0a167: 4.49 ( cpu=4.05 radio=0.432 sensor=0.0110 )
  Uid u0a35: 3.81 ( cpu=2.22 wake=0.0645 radio=1.53 )
  Uid 1036: 2.86 ( cpu=2.86 )
  Uid u0a67: 2.74 ( cpu=2.51 wake=0.0120 wifi=0.205 gps=0.0160 sensor=0.0000576 )
  ......
```
如果 uid < 10000 是系统耗电，如果是 uid >= 10000 是应用耗电。这一步可以判断是哪个 uid 耗电过多。
接下来可以结合应用持有的 wake locks 看，在 batterystats 中有两项 wakelocks。All partial wake locks 和 All kernel wake locks。
```
All partial wake locks:
Wake lock 1000 deviceidle_maint: 55s 242ms (5 times) realtime
Wake lock u0a70 httpGet: 23s 1ms (2 times) realtime
Wake lock u0a70 PTWakeLock: 12s 147ms (24 times) realtime
Wake lock 1000 WakeLock for Memory Clean: 11s 19ms (2 times) realtime
Wake lock 1000 AnyMotionDetector: 10s 26ms (1 times) realtime

......
All kernel wake locks:
Kernel Wake lock wcnss_filter_lock: 5h 9m 11s 31ms (0 times) realtime
Kernel Wake lock PowerManagerService.WakeLocks: 2m 22s 356ms (178 times) realtime
Kernel Wake lock qcom_rx_wakelock: 1m 44s 679ms (1860 times) realtime
Kernel Wake lock qpnp_fg_update_sram: 1m 5s 293ms (3696 times) realtime
Kernel Wake lock qpnp_fg_memaccess: 1m 3s 433ms (72127 times) realtime
Kernel Wake lock wlan        : 11s 603ms (6 times) realtime
Kernel Wake lock NETLINK     : 11s 469ms (12000 times) realtime
......
```
到这一步可以确认哪个 uid 持有哪个 wakelock 导致系统耗电过多。
如果是 App 导致耗电过多，能排除系统原因就可以归到 APP 原因，如不能排除系统原因接着需要分析。
如果是 uid < 10000,则是系统原因，需要继续分析。


### 2 谷歌电量分析工具
通用的耗电信息 battery stats

#### Battery-Historian V1 (needs Python environment)

数据源：         adb shell dumpsys batterystats > data_v1   
分析工具：     执行Python脚本 historian.py   
命令：              Python historian.py –a data_v1 > data_v1.html  
运行：              用谷歌浏览打开 data_v1.html（需要VPN），就可以看到图形化界面   

![v1]( /assets/images/BatteryHistorian_1.png  )


#### Battery-Historian V2 (needs Go environment)


数据源：         adb bugreport data_v2.zip    
分析工具：     使用Battery-Historian V2 Go 项目分析数据   
命令：              go run cmd\battery-historian\battery-historian.go  
运行：              用谷歌浏览打开http://localhost:9999/ （需要VPN），在界面中选择data_v2.zip
![v2]( /assets/images/BatteryHistorian_2.png  )



### 2 other
除了从android batterystats 中分析问题，各厂商可能会加入自己的一些feature，记录耗电和 CPU 实用信息等，可辅助分析异常耗电、发热等问题


