## OpenOCD

OpenOCD provides on-chip programming and debugging support.

## Table of Contents
- [Installation on Ubuntu based distros](#installation-on-ubuntu-based-distros)
- [External references](#external-references)

### Installation  on Ubuntu based distros

```bash
sudo apt install openocd
```

### Running openocd
To run openocd with stlink and the 32F429ZI Discovery Board.

```bash
openocd -f /usr/share/openocd/scripts/interface/stlink-v2.cfg \
        -f /usr/share/openocd/scripts/target/<your board.cfg>
```

**Check for openocd ports**
```bash
sudo netstat -tulpn | grep openocd
```

### Installation of gdb for Arm debugging

```bash
sudo apt install gdb-multi-arch
```

Start gdb using an .elf file.
```bash
gdb-multiarch <./filename.elf>
```
Connect gdb to openocd port at: `localhost:3333`

```bash
(gdb) target extended-remote localhost:3333
```
Useful `gdb` command examples.
```bash
// configure window
(gdb) lay next
(gdb) lay next
// add a breakpoint at main function start
(gdb) b main
// continue
(gdb) c
// step
(gdb) s
// add a breakpoint at line 19
(gdb) b 19
// delete a breakpoint at line4
(gdb) d 4
// see register
(gdb) info registers
(gdb) lay next
(gdb) refresh
// check a memory address
(gdb) x/32wx 0x8000215 
```

[Top](#table-of-contents)
## External references
- [Open On-Chip Debugger](https://openocd.org/pages/getting-openocd.html)
- [youtube.com | Low Level Learning | This Is 100% How You Should Be Debugging | How to Use OpenOCD to Debug Embedded Software with GDB](https://www.youtube.com/watch?v=_1u7IOnivnM)
- [Debugging an embedded ARM device in Visual Studio](https://devblogs.microsoft.com/cppblog/debugging-an-embedded-arm-device-in-visual-studio/)

