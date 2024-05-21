## ST-Link/V2

## Table of Contents
- [Installation on Ubuntu based distros](#installation-on-ubuntu-based-distros)
- [External references](#external-references)

### Installation  on Ubuntu based distros

To flash and debug using the ST-Link/V2 port of the 32F429I Discovery Board on a Ubuntu based distro command line, first install `stlink-tools`:

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

[Top](#table-of-contents)
## External references
- [ST-LINK/V2 in-circuit debugger/programmer for STM8 and STM32](https://www.st.com/en/development-tools/st-link-v2.html)