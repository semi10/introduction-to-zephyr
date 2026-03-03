# Toolchain & Debugger Notes

## OpenOCD via Zephyr SDK

**Binary path:**
```
/opt/toolchains/zephyr-sdk-0.16.8/sysroots/x86_64-pokysdk-linux/usr/bin/openocd
```

**Version:** `0.11.0+dev-00728-gb6f95a16c` (built 2024-05-29)

**Scripts root:**
```
/opt/toolchains/zephyr-sdk-0.16.8/sysroots/x86_64-pokysdk-linux/usr/share/openocd/scripts/
```

### ST-Link interface configs available
| File | Description |
|---|---|
| `interface/stlink.cfg` | Generic (auto-detect version) |
| `interface/stlink-v1.cfg` | ST-Link v1 |
| `interface/stlink-v2.cfg` | ST-Link v2 |
| `interface/stlink-v2-1.cfg` | ST-Link v2.1 (Nucleo on-board) |
| `interface/stlink-dap.cfg` | ST-Link in DAP mode |

### West runner integration
- Runner script: `/opt/toolchains/zephyr/scripts/west_commands/runners/openocd.py`
- `west flash --runner openocd`
- `west debug --runner openocd`

### Notes
- **RULE: Never run `which openocd` or search PATH for tools — always use known paths from this file**
- `openocd` is NOT on system PATH — must use full path or set via west runner config
- USB passthrough required in containerized environments for physical ST-Link access
- Board-specific OpenOCD configs live under `/opt/toolchains/zephyr/boards/<vendor>/<board>/support/openocd.cfg`

## ST-Link USB Passthrough (Podman)

**Container type:** Podman (detected via `systemd-detect-virt`)
**ST-Link detected on host:** `0483:3748` at `/dev/bus/usb/001/010` (bus 1, device 10)
**Problem:** No `/dev/bus/usb` in container; container lacks `CAP_MKNOD` and `CAP_SYS_ADMIN`

### Fix: restart container with device passthrough
```bash
podman run --device /dev/bus/usb/001:/dev/bus/usb/001 <image> ...
```
Note: pass the full bus (`001/`) not just the device, so it survives reconnects.

### Alternative: TCP bridge (no restart needed)
Run OpenOCD on host, connect from container via TCP:
- Host: `openocd -f interface/stlink.cfg -f target/stm32f4x.cfg`
- Container GDB: `target remote host.containers.internal:3333`

### Host udev rule for ST-Link (non-root access)
```
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="3748", MODE="0666", GROUP="plugdev"
```
File: `/etc/udev/rules.d/70-stlink.rules`

## Zephyr SDK

- **SDK root:** `/opt/toolchains/zephyr-sdk-0.16.8/`
- **Zephyr RTOS source:** `/opt/toolchains/zephyr/` (`ZEPHYR_BASE`)
- **SDK version:** 0.16.8
- **Zephyr version:** v4.0.0 (from west.yml)
