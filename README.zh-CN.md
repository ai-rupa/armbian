# Blueberry 开发板

[English](README.md) | **简体中文**

Rupa 品牌下的开源开发板系列，基于 [Armbian Build Framework](https://github.com/armbian/build) 构建。本仓库在 Armbian 基础上增加了 Blueberry 各板型的配置与设备树绑定，用于产出可直接烧写的 Ubuntu 固件。

---

## 一、支持的板型

| `BOARD=` | 型号 | SoC | 主要接口 |
|---|---|---|---|
| `blueberry-rk3588-edge` | Blueberry Edge | RK3588 | 双千兆、HDMI 输出 + HDMI 输入、SATA、M.2 NVMe、RS485/RS232、4G |
| `blueberry-rk3588-minipc` | Blueberry MiniPC | RK3588 | 单千兆、双 HDMI 输出、M.2、SATA |
| `blueberry-rk3576-edge` | Blueberry RK3576 Edge | RK3576 | 双千兆、HDMI + DP 输出、HDMI 输入、MIPI 屏、RS485、4G |
| `blueberry-rk3568-edge` | Blueberry Edge（RK3568） | RK3568 | 规划中，暂不可构建 |

三块可构建板型统一使用 `vendor` 内核分支（Rockchip BSP 6.1），串口控制台为 `ttyS2 @ 1500000 8N1`。

---

## 二、构建固件

### 环境要求

- Linux（Ubuntu 22.04/24.04 推荐），或 macOS / Windows + Docker Desktop
- Docker 20.10+（需支持 `--privileged`）
- 磁盘 ≥ 50 GB，内存 ≥ 8 GB

### 获取源码

```bash
git clone https://github.com/ai-rupa/armbian.git
cd armbian
git checkout rupa_armbian_v26.08
```

### 构建

服务器版（无桌面）：

```bash
./compile.sh build \
  BOARD=blueberry-rk3576-edge \
  BRANCH=vendor \
  BUILD_DESKTOP=no \
  BUILD_MINIMAL=no \
  KERNEL_CONFIGURE=no \
  RELEASE=noble
```

桌面版（XFCE）：

```bash
./compile.sh build \
  BOARD=blueberry-rk3576-edge \
  BRANCH=vendor \
  BUILD_DESKTOP=yes \
  BUILD_MINIMAL=no \
  DESKTOP_ENVIRONMENT=xfce \
  DESKTOP_TIER=minimal \
  KERNEL_CONFIGURE=no \
  RELEASE=noble
```

只编内核或 u-boot：

```bash
./compile.sh kernel BOARD=blueberry-rk3588-edge BRANCH=vendor KERNEL_CONFIGURE=yes
./compile.sh uboot BOARD=blueberry-rk3588-edge BRANCH=vendor
```

### 产物

```
output/images/
├── Armbian-unofficial_26.08.0-trunk_Blueberry-rk3576-edge_noble_vendor_6.1.115_xfce_desktop.img
├── ...img.sha    # SHA256
└── ...img.txt    # 镜像内容摘要
```

校验后即可烧写：

```bash
cd output/images && shasum -a 256 -c *.img.sha
```

支持 microSD / eMMC / NVMe / SPI NOR 启动，烧写方式按板型选择 `dd`、UMS 模式或 `rkdeveloptool`（也可用 `ENABLE_EXTENSIONS=rkdevflash` 让构建完成后自动烧写）。

---

## 三、注意事项

- **内核与 u-boot 源码地址**写在 `config/sources/families/*.conf` 与各板型 `.conf` 中。使用自有镜像站时改这两处即可，其余逻辑无需改动。
- **本地定制放 `userpatches/`**：`config-custom.conf` 保存常用参数组合，`customize-image.sh` 与 `overlay/` 用于预置用户、软件包和开机脚本。该目录下的改动不会被提交到上游。
- **不要修改 `lib/`**：那是 Armbian 框架核心，改动会导致每次同步上游都要重新处理。

---

## 许可证

本仓库遵循 **GPL-2.0**，详见 [LICENSE](LICENSE)。本项目基于 Armbian Build Framework 构建，非 Armbian 官方项目，产物以 `Armbian-unofficial_*` 命名。

上游 Armbian 原有说明保留在 [README.armbian.md](README.armbian.md)。
