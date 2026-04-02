## 构建zephyr镜像

下面这条命令会基于 `/path/to/zephyr` 编译 `tests/benchmarks/latency_measure`：

```bash
export PATH=/tmp/zephyr-pyenv/bin:$PATH
export ZEPHYR_BASE=/path/to/zephyr
export ZEPHYR_SDK_INSTALL_DIR=/tmp/zephyr-sdk-1.0
export CROSS_COMPILE=/path/to/zephyr-sdk-0.16.5-1/aarch64-zephyr-elf/bin/aarch64-zephyr-elf-
export CCACHE_DIR=/tmp/ccache
export CCACHE_TEMPDIR=/tmp/ccache/tmp

mkdir -p /tmp/zephyr-latency-build/Kconfig /tmp/zephyr-latency-build/zephyr

(cd /path/to/zephyr && \
  /tmp/zephyr-pyenv/bin/python scripts/zephyr_module.py \
    --kconfig-out /tmp/zephyr-latency-build/Kconfig/Kconfig.modules \
    --sysbuild-kconfig-out /tmp/zephyr-latency-build/Kconfig/Kconfig.sysbuild.modules \
    --cmake-out /tmp/zephyr-latency-build/zephyr/zephyr_modules.txt \
    --settings-out /tmp/zephyr-latency-build/zephyr/settings.txt \
    -z /path/to/zephyr)

cmake -GNinja \
  -B /tmp/zephyr-latency-build \
  -S /path/to/zephyr/tests/benchmarks/latency_measure \
  -DBOARD=phytiumpi_axvisor_guest \
  -DBOARD_ROOT=/path/to/zephyr-boards \
  -DCONF_FILE=/path/to/zephyr-boards/phytiumpi_axvisor_guest.conf \
  -DZEPHYR_TOOLCHAIN_VARIANT=cross-compile \
  -DPython3_EXECUTABLE=/tmp/zephyr-pyenv/bin/python \
  -DDTC=/usr/bin/dtc

cmake --build /tmp/zephyr-latency-build
```

###  产物位置

编译完成后，主要产物在：

- `/tmp/zephyr-latency-build/zephyr/zephyr.bin`
- `/tmp/zephyr-latency-build/zephyr/zephyr.elf`
- `/tmp/zephyr-latency-build/zephyr/zephyr.dts`

仓库里还保留了一份方便使用的拷贝：

- [zephyr-phytiumpi-latency.bin](/path/to/tmp/zephyr-phytiumpi-latency.bin)
- [zephyr-phytiumpi-latency.elf](/path/to/tmp/zephyr-phytiumpi-latency.elf)
- [zephyr-phytiumpi-latency.dtb](/path/to/tmp/zephyr-phytiumpi-latency.dtb)
