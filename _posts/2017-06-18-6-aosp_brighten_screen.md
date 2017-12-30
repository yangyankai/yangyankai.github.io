---

layout: post

category : post

tagline: "Supporting tagline"

tags : [brighten screen , beginner]

---

{% include JB/setup %}

### PhoneWindowManage.java  :

KEYCODE_POWER ->  interceptPowerKeyDown(...) -> wakeUpFromPowerKey() -> wakeUp()  -> mPowerManager.wakeUp(...)

### PowerManagerService:

wakeUp(..) -> wakeUpInternal(...) -> updatePowerStateLock -> updateDisplayPowerStateLocked -> mDisplayManagerInternal.requestPowerState(...)

### DiaplayPowerController.java:
requestPowerState(...) -> sendUpdatePowerStateLocked() -> sendMessage(MSG_UPDATE_POWER_STATE) -> updatePowerState() -> animateScreenStateChange -> setScreenState()

picture from other person's blog
![brighten_screen](/assets/images/brighten_screen.png)

