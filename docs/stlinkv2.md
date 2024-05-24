## ST-Link/V2

## Table of Contents
- [Installation on Ubuntu based distros](#installation-on-ubuntu-based-distros)
- [External references](#external-references)

### Installation  on Ubuntu based distros

To flash and debug using the ST-Link/V2 port of the 32F429I Discovery Board on a Ubuntu based distro command line, first install [stlink-tools](https://github.com/stlink-org/stlink?tab=readme-ov-file) an open source version of the STMicroelectronics STLINK Tools:

```bash
$ sudo apt install stlink-tools
```

Test the installation:
```bash
$ st-info --version
v1.7.0
```

### Useful commands
Firstly, connect the 32F429I Discovery Board to the Ubuntu based development environment.

**Probe the hardware**
```bash
st-info probe
```
**Flash a .bin file**
Change to the folder containing the .bin file to flash. In terminal type:
```bash
st-flash --reset write <filename.bin> <mem location hex>
```

### st-util

Run GDB server on localhost port `4242`. This is the default port for `st-util`.

```bash
$ gdb-multiarch -q ./awesomesauce.elf \
                -ex "target extended-remote localhost:4242"
```

Connect to it using `st-util`.

```bash
$ st-util
st-util
2024-05-22T13:34:56 INFO common.c: F42x/F43x: 256 KiB SRAM, 2048 KiB flash in at least 16 KiB pages.
2024-05-22T13:34:56 INFO gdb-server.c: Listening at *:4242...
2024-05-22T13:34:56 INFO common.c: F42x/F43x: 256 KiB SRAM, 2048 KiB flash in at least 16 KiB pages.
2024-05-22T13:34:56 INFO gdb-server.c: Found 6 hw breakpoint registers
2024-05-22T13:34:56 INFO gdb-server.c: GDB connected.
```

### Makefile target to start st-link and gdb-multiarch together

```Makefile
debug:
    st-util & gdb-multiarch -q ./awesomesauce.elf -ex "target extended-remote localhost:4242"
```
### kill st-util port 4242

```bash
kill $(lsof -t -i:4242)
```

[Top](#table-of-contents)
## External references
- [stlink-tools | Open source version of the STMicroelectronics STLINK Tools](https://github.com/stlink-org/stlink?tab=readme-ov-file)
- [ST-LINK/V2 in-circuit debugger/programmer for STM8 and STM32](https://www.st.com/en/development-tools/st-link-v2.html)
- [st-flash | Man Page | Flash binary files to STM32 device](https://www.mankier.com/1/st-flash)
- [st-util | Man Page | Run GDB server to interact with STM32 device](https://www.mankier.com/1/st-util)
