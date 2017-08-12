---

layout: post

category : post

tagline: "Supporting tagline"

tags : [battery , beginner]

---

{% include JB/setup %}

### 1.电量分类BatterySipper 

```
public enum DrainType {
        IDLE,  // 手机待机
        CELL,  // 移动网络待机
        PHONE,    // 语音通话
        WIFI,           //   WiFi
        BLUETOOTH, //  蓝牙
        FLASHLIGHT, //闪光灯
        SCREEN,      屏幕
        APP,          // App，软件
        USER, // UserManager
        UNACCOUNTED, // 系统耗电比计算的多
        OVERCOUNTED, // 系统耗电比计算的少
        CAMERA    // 摄像头
}
```


电量统计各类型在其他语言中如何如何命名，参看考 Settings 中的下面代码，找到xml/value 中 string 对应翻译

BatterySipper
```

        switch (sipper.drainType) {
            case IDLE: 
                name = context.getResources().getString(R.string.power_idle);
                iconId = R.drawable.ic_settings_phone_idle;
                break;
            case CELL:
                name = context.getResources().getString(R.string.power_cell);
                iconId = R.drawable.ic_settings_cell_standby;
                break;
            case PHONE:
                name = context.getResources().getString(R.string.power_phone);
                iconId = R.drawable.ic_settings_voice_calls;
                break;
            case WIFI:  
                name = context.getResources().getString(R.string.power_wifi);
                iconId = R.drawable.ic_settings_wifi;
                break;
            case BLUETOOTH: 
                name = context.getResources().getString(R.string.power_bluetooth);
                iconId = R.drawable.ic_settings_bluetooth;
                break;
            case SCREEN:
                name = context.getResources().getString(R.string.power_screen);
                iconId = R.drawable.ic_settings_display;
                break;
            case FLASHLIGHT:
                name = context.getResources().getString(R.string.power_flashlight);
                iconId = R.drawable.ic_settings_display;
                break;
            case APP:
                name = sipper.packageWithHighestDrain;
                break;
            case USER: {
                UserInfo info = um.getUserInfo(sipper.userId); 
                if (info != null) {
                    icon = Utils.getUserIcon(context, um, info);
                    name = Utils.getUserLabel(context, info);
                } else {
                    icon = null;
                    name = context.getResources().getString(
                            R.string.running_process_item_removed_user_label);
                }
            } break;
            case UNACCOUNTED:
                name = context.getResources().getString(R.string.power_unaccounted);
                iconId = R.drawable.ic_power_system;
                break;
            case OVERCOUNTED:
                name = context.getResources().getString(R.string.power_overcounted);
                iconId = R.drawable.ic_power_system;
                break;
```


### 2软硬件耗电统计
硬件耗电
```
                case PHONE :
                case SCREEN : 屏幕
                case FLASHLIGHT:
                case WIFI :
                case BLUETOOTH:
                case IDLE:
                case CELL:
                        isMisc = true;
                        percentOfTotal = (sipper.totalPowerMah * 100)/ hardwareTotal;
                        Log.d(TAG,"percentOfTotal : "+hardwareTotal);
                    break;
```
软件耗电
```
                case APP:
                    break;
                default:
                    break;
```




