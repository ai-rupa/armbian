# Blueberry Boards

**English** | [简体中文](README.zh-CN.md)

Open-source development boards under the **Rupa** brand, built on the [Armbian Build Framework](https://github.com/armbian/build). This repository adds Blueberry board definitions and device-tree bindings on top of Armbian to produce ready-to-flash Ubuntu images.

---

## 1. Supported Boards

| `BOARD=` | Model | SoC | Key interfaces |
|---|---|---|---|
| `blueberry-rk3588-edge` | Blueberry Edge | RK3588 | Dual GbE, HDMI out + HDMI in, SATA, M.2 NVMe, RS485/RS232, 4G |
| `blueberry-rk3588-minipc` | Blueberry MiniPC | RK3588 | Single GbE, dual HDMI out, M.2, SATA |
| `blueberry-rk3576-edge` | Blueberry RK3576 Edge | RK3576 | Dual GbE, HDMI + DP out, HDMI in, MIPI display, RS485, 4G |
| `blueberry-rk3568-edge` | Blueberry Edge (RK3568) | RK3568 | Planned — not buildable yet |

The three buildable boards all use the `vendor` kernel branch (Rockchip BSP 6.1). The serial console is `ttyS2 @ 1500000 8N1`.

---

## 2. Building Images

### Requirements

- Linux (Ubuntu 22.04 / 24.04 recommended), or macOS / Windows with Docker Desktop
- Docker 20.10+ (must support `--privileged`)
- At least 50 GB of disk space and 8 GB of RAM

### Get the source

```bash
git clone https://github.com/ai-rupa/armbian.git
cd armbian
git checkout rupa_armbian_v26.08
```

### Build

Server image (no desktop):

```bash
./compile.sh build \
  BOARD=blueberry-rk3576-edge \
  BRANCH=vendor \
  BUILD_DESKTOP=no \
  BUILD_MINIMAL=no \
  KERNEL_CONFIGURE=no \
  RELEASE=noble
```

Desktop image (XFCE):

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

Kernel or U-Boot only:

```bash
./compile.sh kernel BOARD=blueberry-rk3588-edge BRANCH=vendor KERNEL_CONFIGURE=yes
./compile.sh uboot BOARD=blueberry-rk3588-edge BRANCH=vendor
```

### Output

```
output/images/
├── Armbian-unofficial_26.08.0-trunk_Blueberry-rk3576-edge_noble_vendor_6.1.115_xfce_desktop.img
├── ...img.sha    # SHA256
└── ...img.txt    # image content summary
```

Verify the checksum, then flash:

```bash
cd output/images && shasum -a 256 -c *.img.sha
```

Booting from microSD / eMMC / NVMe / SPI NOR is supported. Flash with `dd`, UMS mode, or `rkdeveloptool` depending on the board — or add `ENABLE_EXTENSIONS=rkdevflash` to flash automatically once the build finishes.

---

## 3. Notes

- **Kernel and U-Boot source URLs** live in `config/sources/families/*.conf` and in each board's `.conf`. If you use your own mirror, change only those entries; no other logic needs touching.
- **Put local customizations in `userpatches/`**: `config-custom.conf` holds your usual parameter combinations, while `customize-image.sh` and `overlay/` preset users, packages, and boot scripts. Changes there are never pushed upstream.
- **Do not modify `lib/`**: it is the Armbian framework core, and any change there has to be reapplied on every upstream sync.

---

## License

This repository is licensed under **GPL-2.0**; see [LICENSE](LICENSE). It is built on the Armbian Build Framework and is not an official Armbian project, so images are named `Armbian-unofficial_*`.

Upstream Armbian documentation is preserved in [README.armbian.md](README.armbian.md).
