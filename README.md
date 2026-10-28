# AIC8800M80 Bluetooth reconnect workaround

用于 AIC8800M80 USB Wi-Fi / Bluetooth 二合一网卡的已签名固件加载驱动替换记录。

## 驱动来源与范围

- 来源：https://www.brostrend.com/pages/wb2p-download
- 官方产品芯片说明：https://www.brostrend.com/pages/wb2p-specification
- 驱动提供商：AIC
- 文件：aicloadfw.Inf / aicloadfw.cat / aicloadfw.Sys
- 版本：12.60.30.224，日期：2026-02-02
- INF 声明的硬件 ID：USB\VID_A69C&PID_8D80
- 架构：Windows x64；官方产品支持 Windows 10/11。本次行为测试仅在 Windows 10 22H2 x64 上完成。

本仓库记录厂商签名的现成固件加载驱动替换方法，没有修改 SYS、INF 或签名，也不是自研蓝牙驱动。由于原安装包中未发现明确的再分发许可，本仓库不镜像第三方驱动二进制；请从上方官方入口下载并离线提取 win10_x64 下的三个 aicloadfw 文件，放入自己创建的 driver 目录。第三方驱动权利归原权利人所有。

## 已验证的情形

一只柯漫尼品牌网卡在配对 HUAWEI FreeClip 2 后，首次连接正常，但耳机入盒再取出后回连失败。系统出现 BTHUSB Event ID 16 认证错误。

将加载驱动从 7.55.16.718 换为 12.60.30.224，断电重插、重新配对后，用户确认首次连接及连续三次入盒/取出回连正常；测试窗口内没有新增 Event ID 16。

更新同时改变了本地蓝牙地址，因此不能把效果完全归因于驱动版本。没有长期、多台设备、其他贴牌型号或其他耳机的验证结论。

## 兼容性

只有加载阶段硬件 ID 与上述 ID 匹配，才属于这个 INF 声明的适用范围。正常工作时的本次设备 ID 为 USB\VID_368B&PID_8D81；蓝牙接口 MI_00，Wi-Fi 接口 MI_02。

同品牌、相同外观、AX900 或蓝牙 5.4 字样均不足以证明兼容。不同 OEM、板卡版本仍需分别验证。

## 安装与回退

本仓库不含原电脑的自动修复任务、配对清理脚本和硬编码 oem 编号。不要直接使用其他电脑的 oem15.inf 或 oem49.inf 编号。

1. 在设备管理器核对硬件 ID，并确定当前 aicloadfw 驱动实际发布名称（oemNN.inf）。
2. 管理员终端先备份当前驱动：`pnputil /export-driver oemNN.inf C:\AIC-Driver-Backup`，将 oemNN.inf 换为实际名称。确认成功再继续。
3. 管理员终端进入本仓库，运行：`pnputil /add-driver .\driver\aicloadfw.Inf /install`。检查命令结果；不要强制安装到其他硬件 ID。
4. 拔出网卡，等待约 20 秒，重新插入。检查加载驱动是否实际生效。若没有生效，停止操作并收集设备状态，不要随意删除驱动。
5. 本地蓝牙地址可能变化，已有蓝牙设备可能需要重新配对。播放音频后，连续测试耳机入盒合盖 10 秒再取出至少三次。
6. 回退时先确定新驱动当前的实际 oem 名称，使用 `pnputil /delete-driver oemNN.inf /uninstall`，再用 `pnputil /add-driver C:\AIC-Driver-Backup\实际备份目录\aicloadfw.inf /install` 恢复原版，随后断电重插。不要删除不确定归属的驱动包。

本次仅替换固件加载驱动，保留原 Wi-Fi 驱动和 Windows 蓝牙驱动。未验证 BIOS、USB 硬件故障或所有断连问题都能由此解决。

## 文件校验

见 SHA256SUMS.txt。这些是本次验证版本的文件哈希；官方后续版本可能变化，不应混淆为同一版本。本仓库不包含本机用户名、设备序列号、蓝牙地址、配对日志、反编译结果或原电脑专用安装/回退脚本。