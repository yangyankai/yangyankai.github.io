---


layout: post


category : post


tagline: "Supporting tagline"


tags : [ AOSP, Automatic-Brightness, beginner]


---

{% include JB/setup %}


源码阅读：Android 自动背光机制

0 刷新机制概览（使用handler实现）
1 传感器和缓存
2 计算buffer中的加权后的光强。
3 通过光强计算亮度
4 更新亮度
5 流程总结
附: RampAnimator.java 背光动画

DisplayPowerController 中调用AutomaticBrightnessController 实现自动背光，AutomaticBrightnessController 采集缓存和计算自动背光
### 0 刷新机制概览（使用handler实现）


![picture-1 refresh](/assets/images/AOSP-automatic-brightness-1.png)



  
### 1 传感器和缓存

1-1从传感器获取光强，单位Lux，并记录时间。 时间和光强作为键值对保存到缓存中
  private final SensorEventListener mLightSensorListener = new SensorEventListener() {
        @Override
        public void onSensorChanged(SensorEvent event) {

                final long time = SystemClock.uptimeMillis();
                final float lux = event.values[0];
                handleLightSensorEvent(time, lux);
            }
        }
}

1-2 缓存AmbientLightRingBuffer 

     AutomaticBrightnessController 中定义了一个AmbientLightRingBuffer环形缓存。
里面存放键值对 （时间和光强）
     private float[] mRingLux;
     private long[] mRingTime;
这里的时间是：SystemClock.uptimeMillis()//从boot开始的毫秒数

     添加元素
     public void push(long time, float lux) { }

     每次给buffer中放数据的时候回清理掉5s以前的数据，只保存5s内的数据。
     public void prune(long horizon) { }

     缓存时间为 horizon =time - AMBIENT_LIGHT_HORIZON； 就是最近5000ms的光强缓存。（谷歌原生为最近10000ms缓存）

注意：
     是最近5s的缓存。一般来说光敏传感器最快为100ms接受几个lux，buffer中有50个数据，但是当光强稳定的时候不在接收数据。只有周围光强变化的时候，这个事件 public void onSensorChanged(SensorEvent event) { } 才会触发。这时候buffer中数据个数会小于50个。

### 2 计算buffer中的加权后的光强。
![picture-3 refresh](/assets/images/AOSP-automatic-brightness-2.png)

                                   

      Lux i 是buffer中的光强，Weighti是对应光强的权重。Weighti：Luxi的Timei通过积分后算出来的
           
               Weighti=Time1*（Time1*0.5 + b）-Time2*（Time2*0.5 + b）

     mBrighteningLuxThreshold = mAmbientLux * (1.0f + BRIGHTENING_LIGHT_HYSTERESIS); // 增亮阈值
     mDarkeningLuxThreshold = mAmbientLux * (1.0f - DARKENING_LIGHT_HYSTERESIS);       // 变暗阈值
如新计算出来的 mAmbientLux 大于的mBrighteningLuxThreshold或小于mDarkeningLuxThreshold，则去计算brightness并更新亮度。

     开始下一次递归： mHandler.sendEmptyMessageAtTime(MSG_UPDATE_AMBIENT_LUX, nextTransitionTime);
递归周期：nextTransitionTime 是根据buffer中的数据与增亮阈值和变暗阈值计算出来的。 
遍历检测buffer中是否数据达到阈值，再根据变亮mBrighteningLightDebounceConfig 或者变暗延时mDarkeningLightDebounceConfig 和;mLightSensorRate，计算出最终nextTransitionTime；


### 3 通过光强计算亮度

3-1 计算value，value范围 0 - 1
 float value = mScreenAutoBrightnessSpline.interpolate(mAmbientLux);
mScreenAutoBrightnessSpline.interpolate()//BSP首先给出一些点的映射，这个函数会自动补齐其他的点。得出光强和lux的映射关系。
BSP给出点的映射所在目录： platform/LINUX/android/device/fancymaker/z2x/overlay/frameworks/base/core/res/res/values.config_autobrightness.xml
 
3-2 计算亮度，brighness范围0-255
亮度 brightness=value * 255;

### 4 更新亮度
mCallbacks.updateBrightness();

### 5 流程总结

![picture-3 refresh](/assets/images/AOSP-automatic-brightness-3.png)


### 附：
### RampAnimator.java 背光动画

速度：                   mRate，表示帧数，亮度为0-255，一共256帧
过渡间隔：             Math.abs(target - mCurrentValue);
过渡时间：             Math.abs(target - mCurrentValue) / mRate
举个例子：
     亮度从100到200，帧数mRate为50.这时候过渡时间为t =（200-100）/50 = 2s,需要两秒完成这个动画。

注意：
当正在发生一个动画的时候，出现一个新的动画，这时候采取最接近当前动画的一个去更新。
举个例子：
     a. 刚开始亮度为0；
     b. 第一个动画到200；
     c. 当b中的动画没有完成时，如果有一个新的动画x。
          若 x <= 200,则直接到x，不再向200过渡；
          若 x> 200,则匀速经过200，然后匀速达到x；


### 动画机制来自递归：
1： 
     postAnimationCallback(){
          mAnimationCallback()；
     }
2：
     mAnimationCallback(){
          if(mTargetValue!=mCurrentValue){
               postAnimationCallback
          }  
     }


### 自动背光修改亮度流程
AutomaticBrightnessController 

RampAnimator.java  mProperty.setValue(mObject, mCurrentValue)

DisplayPowerController.java 中实例化 new RampAnimator<DispalyPowerState>(mPowerState,DispalyPowerState.SCREEN_BRIGHTNESS);


DispalyPowerState.java  DispalyPowerState.setValue(){object.setScreenBrightness(value)};  ...... ->  listener.run()  run(){  requestDisplayState()}

ZuiDispalyPowerState.java setBrightnessChangedListener_ZUK( ){mBrightnessChangedListener.onBrightnessChanged(Brightness);}

ZuiDisplayPowerControlle.java  setBrightnessChangedListener_ZUK(mZuiBrightnessChangedListener )   =new ZuiBrightnessChangedListener{
onBrightnessChanghe(){
//修改数据库,字段为：Settings.System.SCREEN_BRIGHTNESS
}

}





