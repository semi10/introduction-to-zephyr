# Workspace Structure

## Layout
```
/workspace/
├── west.yml          # West manifest (Zephyr v4.0.0)
├── apps/             # Sample/exercise applications (01_blink … 12_solution_custom_board)
├── boards/custom/    # Custom board definitions
└── modules/          # button, mcp9808, say_hello
```

## West manifest (`west.yml`)
- Zephyr remote: `https://github.com/zephyrproject-rtos`
- Revision: `v4.0.0`
- Allowed HALs: `cmsis`, `hal_stm32`, `hal_xtensa`, `hal_espressif`, `hal`, `mbedtls`

## Environment variables
| Variable | Value |
|---|---|
| `ZEPHYR_BASE` | `/opt/toolchains/zephyr` |
| `ZEPHYR_SDK_INSTALL_DIR` | `/opt/toolchains` |
