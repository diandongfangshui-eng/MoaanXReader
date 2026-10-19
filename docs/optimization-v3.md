# 墨案 X × 番茄小说全面性能优化（v3）

用户已在墨案 X 真机确认：**番茄小说 6.5.9.32 ARM32 原版**可以登录、同步书架、在线阅读、使用番茄官方听书。现阶段不修改、重签名、传播原厂 APK，不破坏以上四项。

## 设备优先措施（无需 PC）

1. 墨案通知栏/设备设置中的刷新模式设置为 **快速刷新**，而非普通刷新。历史设备资料显示 InkPad X 通常只有两档、没有应用级独立刷新模式。若字迹残影明显，手动全刷。
2. 番茄阅读设置中若提供“无翻页动画/覆盖翻页”，选择无动画；操作方式由连续滑动改为点击整页翻页。
3. 记录并关闭 Android 三项动画缩放（窗口/过渡/动画时长）；这只会改善系统级转场，不能解决所有应用内部动画或 E Ink 物理刷新。
4. 如设备支持冻结后台应用，只冻结闲置的**其他第三方** App；不要冻结番茄、音频服务或系统组件。
5. 保持正常联网、书架同步和听书后台权限；不要删除番茄数据、禁止联网或关停与音频相关的后台服务。

参考设备资料：
- https://www.igao7.com/news/202007/5MAcjUb8L5rI4ivx.html
- https://post.smzdm.com/p/a99v38g0/
- https://sspai.com/post/70390

## 有 PC 的测量与进阶优化

已制作 `MoaanX_Tomato_Optimization_v3.zip`，可通过本次 ChatGPT 会话下载。包含 Windows CMD/PowerShell、Linux/macOS Bash 和中文操作说明。

动作：
- `check`：检查设备型号、系统、CPU ABI、安装包版本、当前三项动画缩放、可用存储空间
- `start`：重置 gfxinfo 计数（不清数据），开始同场景测试
- `capture before`：记录优化前 App PSS、gfxinfo、CPU
- `optimize`：备份原值并关闭三项 Android 系统动画，失败自动恢复
- `capture after`：重复同操作并记录指标
- `restore`：恢复系统动画原始设置
- `compile-profile`（可选，人工 YES 确认）：调用 `adb shell cmd package compile -m speed-profile -f com.dragon.read`，请求 ART 只编译热点代码；需要合适存储空间、电池和可用热点信息，效果不保证
- `reset-code`（可选）：`adb shell cmd package compile --reset com.dragon.read`，重置应用手动编译状态至系统默认策略，并非逐字节还原之前编译缓存

Android 官方资料：
- https://source.android.google.cn/docs/core/runtime/jit-compiler?hl=en
- https://developer.android.com/tools/dumpsys

**验证状态**：Bash 脚本在模拟 ADB 环境已测试 check→start→capture→optimize→compile-profile→reset-code→restore，且还原三个原始设置值；PowerShell 脚本尚无 Windows 真机验证。**尚未取得墨案 X 的优化前后数据，不能宣称实机加速百分比。**

不要开启全局后台进程限制、随意禁用本包 Activity/Service、清除应用数据或重打包番茄 APK。墨水屏硬件刷新延迟无法仅靠关闭软件动画根除。
