# cmcc-a10-immortalwrt-hanwckf-firmware-build

基于 [hkint/xiaomi-ax3000t-immortalwrt-hanwckf-firmware-build](https://github.com/hkint/xiaomi-ax3000t-immortalwrt-hanwckf-firmware-build) 的 GitHub Actions 构建方式，为 **CMCC A10（中国移动 A10）** 自动编译 [hanwckf](https://github.com/hanwckf) 的 ImmortalWrt 固件及配套 U-Boot。

## 设备与固件说明

- 源码：`hanwckf/immortalwrt-mt798x`（`openwrt-21.02` 分支，5.4 内核，闭源 MTK 驱动）
- 设备：CMCC A10（MT7981 / Filogic 820，256MB 内存，128MB SPI-NAND），源码原生支持（`Device/cmcc_a10`）
- 默认管理地址：`192.168.31.1`（如需修改请看“自定义”一节）
- 内置软件包与 AX3000T 参考工程保持一致（MTK WiFi 闭源驱动、TurboACC-MTK、passwall/ssr-plus、Argon 主题等），见 [.config](.config)
- 构建产物：`factory.bin`（从官方或其他固件刷入时使用）、`sysupgrade.bin`（ImmortalWrt 内升级使用）

> 注意：AX3000T 与 A10 虽然都是 MT7981 平台，但分区布局与交换机不同，两者固件不可互换。

## 使用方法

1. Fork 本仓库到你的 GitHub 账号。
2. 打开仓库 **Settings → Actions → General**，确认 **Allow all actions and reusable workflows** 已开启。
3. 打开 **Actions** 页面，选择 **Build ImmortalWrt Firmware**，点击 **Run workflow** 手动触发。
4. 首次构建约需 2~3 小时。完成后固件会出现在两个地方：
   - **Artifacts**：`ImmortalWrt_firmware_cmcc-a10_<时间戳>`（`bin/targets/mediatek/mt7981/` 下的镜像文件）
   - **Releases**：自动生成的 `ImmortalWrt_<日期>` Release

（可选）同时运行 **Build U-Boot** 工作流，可构建 `mt7981_cmcc_a10-fip-fixed-parts.bin`。

## 刷机提示

- 从官方固件刷入：参考恩山 [CMCC-A10 免拆刷机教程](https://www.right.com.cn/forum/thread-8300518-1-1.html)，或先刷入 hanwckf U-Boot 后，通过 U-Boot Web 界面（默认 `192.168.1.1`）刷入 `factory.bin`。
- 已在 ImmortalWrt / hanwckf 固件中：在“系统 → 备份/升级”中不保留配置，上传 `sysupgrade.bin` 升级。
- 跨版本升级（尤其是换分区布局/换 U-Boot 时）务必**不要保留配置**。

## 自定义

- **软件包**：修改根目录的 [.config](.config)（例如 `CONFIG_PACKAGE_xxx=y`）。
- **默认 IP / DHCP**：修改 [after-update-custom.sh](after-update-custom.sh)。
- **额外软件源**：在 [before-update-custom.sh](before-update-custom.sh) 中按注释示例添加 `src-git`。
- **U-Boot**：A10 只有单分区布局，没有 multi-layout 变体，因此 [build-u-boot.yml](.github/workflows/build-u-boot.yml) 固定使用 `MULTI_LAYOUT=0 FIXED_MTDPARTS=1` 构建。

## 参考项目

- [hkint/xiaomi-ax3000t-immortalwrt-hanwckf-firmware-build](https://github.com/hkint/xiaomi-ax3000t-immortalwrt-hanwckf-firmware-build)
- [hanwckf/immortalwrt-mt798x](https://github.com/hanwckf/immortalwrt-mt798x)
- [hanwckf/bl-mt798x](https://github.com/hanwckf/bl-mt798x)
