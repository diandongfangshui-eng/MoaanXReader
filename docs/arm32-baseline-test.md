# 墨案 X × 番茄小说 6.5.9.32 — 原版性能测试

## 已经通过的真机功能验收

用户确认 ARM32 官方签名版可以在墨案 X 运行，且登录、书架同步、在线阅读及番茄官方在线听书均正常。

**验收原则：不得牺牲这四项功能。** 不把另行开发的 TXT/EPUB 阅读器当作番茄小说移植成品。

## 优化顺序

1. 记录原版冷启动、书架滚动、正文翻页和在线听书状态。
2. 在无 root、无重新签名条件下，先尝试设备自带的墨水屏刷新策略，并把 Android 三项系统动画缩放调到 0：
   - `window_animation_scale`
   - `transition_animation_scale`
   - `animator_duration_scale`
3. 恢复原值进行同场景前后对照，记录差异；没有性能数据不声称优化成功。
4. 如仍卡顿，再根据 ADB `dumpsys meminfo com.dragon.read`、`am start -W` 等测量结果确定瓶颈，不盲目禁用音频、账号和书架的共用基础服务。
5. 代码/资源重打包将使官方 APK 签名失效，不能承诺重新签名后仍能登录和同步。为保护账号数据，在明确回归测试计划前不制作冒充官方的替换 APK。

## 基础 ADB 指令（无 root）

```sh
adb devices
adb shell getprop ro.product.model
adb shell getprop ro.product.cpu.abi
adb shell settings get global window_animation_scale
adb shell settings get global transition_animation_scale
adb shell settings get global animator_duration_scale
adb shell dumpsys meminfo com.dragon.read
adb shell am start -W -a android.intent.action.MAIN -c android.intent.category.LAUNCHER -p com.dragon.read
```

手动设置零动画（**全设备生效、需先记下原值**）：

```sh
adb shell settings put global window_animation_scale 0
adb shell settings put global transition_animation_scale 0
adb shell settings put global animator_duration_scale 0
```

恢复时用原来记录的值逐项 `settings put global`，不要直接假定以前全是 1。

### 安全边界

测试不需要原版 APK 的源码、不需要 root、不应泄露账号信息；不修改原包签名、不替换或禁用主应用、不中断账号/书架/音频服务，也不公开分发第三方专有 APK。

## 尚未确认

目前不知道卡顿主要出现于：启动 / 书架滑动 / 正文翻页 / 官方听书。需要用户指出最严重场景，才能锁定下一步工程措施。
