# Memfault on nRF5340-DK via VSCode nRF Connect Extension

## Host Environment
- Apple Silicon macOS (M2)
- Tooling: Zephyr SDK or Nordic Toolchain Manager, CMake/Ninja, west
- Console: UART or RTT

Useful links:
- Zephyr SDK releases: https://github.com/zephyrproject-rtos/sdk-ng/releases
- Nordic Toolchain Manager: https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-Desktop
- SEGGER J-Link: https://www.segger.com/downloads/jlink/
- VS Code – nRF Connect extension: https://marketplace.visualstudio.com/items?itemName=nordic-semiconductor.nrf-connect

## Target
- Board: nRF5340-DK (`nrf5340dk_nrf5340_cpuapp`)
- Module: Raytac AN7002Q-DB-5340
  - Product page: https://www.raytac.com/product/ins.php?index_id=139

## Prerequisites
- SEGGER J-Link tools (flash/RTT)
- nRF Connect for Desktop (optional but helpful): https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-Desktop
- VS Code + nRF Connect extension (optional): https://marketplace.visualstudio.com/items?itemName=nordic-semiconductor.nrf-connect
- Zephyr SDK 0.16.x (or use Nordic Toolchain shell): https://github.com/zephyrproject-rtos/sdk-ng/releases
- Python 3.8+

If using Zephyr SDK, export:
```bash
export ZEPHYR_TOOLCHAIN_VARIANT=zephyr
export ZEPHYR_SDK_INSTALL_DIR=~/zephyr-sdk-0.16.5
```

## Project Layout
- App: `blinky1/`
  - Config: `blinky1/prj.conf`
  - Source: `blinky1/src/main.c`
- Memfault SDK (generic Zephyr port): `modules/lib/memfault-firmware-sdk/`

## Build & Flash
```bash
west update
west build -b nrf5340dk_nrf5340_cpuapp blinky1 -p
west flash
```

Open a console:
- UART: your serial terminal of choice
- RTT: J-Link RTT Viewer or `west attach` and RTT tools

## Memfault Integration (Generic Zephyr Port)
Using: `modules/lib/memfault-firmware-sdk/ports/zephyr/`

Key Kconfig in `blinky1/prj.conf`:
- `CONFIG_MEMFAULT=y`
- `CONFIG_MEMFAULT_PROJECT_KEY="..."`
- `CONFIG_MEMFAULT_SHELL=y`
- `CONFIG_UART_CONSOLE=y`
- `CONFIG_MEMFAULT_BUILTIN_DEVICE_INFO_SOFTWARE_VERSION="1.0.0"`
- `CONFIG_MEMFAULT_BUILTIN_DEVICE_INFO_SOFTWARE_TYPE="app"`
- `CONFIG_MEMFAULT_BUILTIN_DEVICE_INFO_HARDWARE_VERSION="nrf5340dk"`

Verify on-device via shell:
```text
memfault help
memfault heartbeat_dump
memfault export
```

## Generic vs NCS Integration
- Generic (this repo): symbols are `CONFIG_MEMFAULT_*` (e.g., `CONFIG_MEMFAULT_PROJECT_KEY`). Portable; you wire transports/storage.
- Nordic NCS: symbols are `CONFIG_MEMFAULT_NCS_*`, extras like `CONFIG_BT_MDS`. Requires building inside an NCS workspace.

Docs/links:
- Memfault Firmware SDK: https://github.com/memfault/memfault-firmware-sdk
- Memfault Quickstart for NCS (for comparison): https://docs.memfault.com/docs/mcu/quickstart-nrf5x-ncs

## References & Videos
- Getting started (YouTube): https://www.youtube.com/watch?v=_bntjXkEdTQ
- Memfault intro/demo (YouTube): https://www.youtube.com/watch?v=-69OiUdhPzQ