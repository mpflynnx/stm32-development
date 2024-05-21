## OpenOCD

OpenOCD provides on-chip programming and debugging support.

## Table of Contents
- [Installation on Ubuntu based distros](#installation-on-ubuntu-based-distros)
- [Running openocd](#running-openocd)
- [gdb-multiarch usage](#gdb-multiarch-usage)
- [View the gpio register memory address](#view-the-gpio-register-memory-address)
- [Using a .gdbinit file to auto connect to openocd port](#using-a-gdbinit-file-to-auto-connect-to-openocd-port)
- [Adding a Makefile task to start openocd and gdb-multiarch together](#adding-a-makefile-task-to-start-openocd-and-gdb-multiarch-together)
- [kill openocd port 3333](#kill-openocd-port-3333)
- [External references](#external-references)

### Installation on Ubuntu based distros

```bash
sudo apt install openocd
```

### Running openocd
To run `openocd` with `stlink`.

Connect the `32F429ZI-Discovery` board to the development environment via usb.

```bash
cd my-project

openocd -f /usr/share/openocd/scripts/interface/stlink.cfg\
 -f /usr/share/openocd/scripts/target/stm32f4x.cfg
```
**Output**
```bash
Open On-Chip Debugger 0.11.0
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
Info : auto-selecting first available session transport "hla_swd". To override use 'transport select <transport>'.
Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Info : clock speed 2000 kHz
Info : STLINK V2J43M28 (API v2) VID:PID 0483:374B
Info : Target voltage: 2.828818
Info : stm32f4x.cpu: hardware has 6 breakpoints, 4 watchpoints
Info : starting gdb server for stm32f4x.cpu on 3333
Info : Listening on port 3333 for gdb connections
```

Check for `openocd` ports using `netstat` command. `netstat` is part of the net-tools package.

- [How to Install netstat Command in Linux](https://www.tecmint.com/install-netstat-in-linux/)

```bash
sudo apt install net-tools
```

Check installation
```bash
netstat -v
```

Find `openocd` ports. 

```bash
sudo netstat -tulpn | grep openocd
```

```bash
tcp        0      0 127.0.0.1:4444          0.0.0.0:*               LISTEN      2872/openocd        
tcp        0      0 127.0.0.1:3333          0.0.0.0:*               LISTEN      2872/openocd        
tcp        0      0 127.0.0.1:6666          0.0.0.0:*               LISTEN      2872/openocd   
```

- 4444 is for telnet
- 3333 is for gdb

### gdb-multiarch usage

Open new terminal to debug project. Assumes package `gdb-multiarch` installed.

Install `gdb-multiarch` a GNU Debugger (with support for multiple architectures).

```bash
sudo apt install gdb-multiarch
```

```bash
cd my-project
gdb-multiarch ./awesomesauce.elf
```

```bash
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./awesomesauce.elf...
(gdb) 
```

Connect gdb to the `openocd` port 3333. The program will break at main function start. The attached hardware will be held in a reset state.

At (gdb) prompt type:

```bash
(gdb) target extended-remote localhost:3333
```

```bash
Remote debugging using localhost:3333
main () at my-project.c:17
17				__asm__("nop");
```

Open more debugging terminal pane using `lay next` command
```bash
(gdb) lay next
(gdb) lay next
```

Set break point at main function. Then type c to continue  program.

```bash
(gdb) b main
(gdb) c
```

The program will continue.

Use Ctrl + c keys to exit the running program.

Set another breakpoint at line 20. The `c` to continue.

```bash
(gdb) b 20
(gdb) c
Continuing.
```

step next line
```bash
(gdb) n
(gdb) s
```

Breakpoint 2, main () at my-project.c:20
```

Close panes, click inside pane press
Ctrl + x, Ctrl + a

'''bash
(gdb) info registers
```

```bash
(gdb) focus next
```

- [gdb split view with code](https://stackoverflow.com/questions/10115540/gdb-split-view-with-code/51301717#51301717)
- [GDB Text User Interface ](http://www.cs.fsu.edu/~baker/ada/gnat/html/gdb_23.html)
- [github.com | gdb-dashboard](https://github.com/cyrus-and/gdb-dashboard)

### View the gpio register memory address

Use `-g3` when compiling, the compiler then adds macros into debug info.

Then in gdb simply type: 

```bash
(gdb) p /x *GPIOG
```

```bash
(gdb) p /t *GPIOG
```

You also can check if the `.elf` contains the macros, using:

```bash
arm-none-eabi-objdump --dwarf=macro xxxx.elf >xxxx.dwarf
```

### Using a .gdbinit file to auto connect to openocd port

We can add the command:

```bash
target extended-remote localhost:3333
```
to a projects `.gdbinit` file to save typing it at the (gdb) prompt.

Inside the folder that will contain the `.elf` file, create file `.gdbinit`.

Add the following line to the file.

```bash
target extended-remote localhost:3333
```

A `~/.config/gdb/gdbinit` file in your home directory controls what other `.gdbinit` files are allowed to load on startup. 

Create file `~/.config/gdb/gdbinit` if it doesn't already exist, then add line

```bash
add-auto-load-safe-path </add/path/to/project/.gdbinit>
```

Substitute the project path with the actual path to projects `.gdbinit` file.

If you have several projects in different directories you can add a line for each of them. When you start `gdb-multiarch` while in a directory, if the master gdbinit file `~/.config/gdb/gdbinit` allows it, gdb will load and process a second .gdbinit file which is in the current working directory. Doing this you can have specific instructions on a per project basis.


- [Gui & command line remote debugging](https://www.96boards.org/blog/gui-command-line-remote-debugging/)


### Adding a Makefile task to start openocd and gdb-multiarch together

:gdb
  openocd -f /usr/share/openocd/scripts/interface/stlink.cfg -f /usr/share/openocd/scripts/target/stm32f4x.cfg & gdb-multiarch -q ./awesomesauce.elf


Run the `make` task with:

```bash
make gdb
```



### kill openocd port 3333

```bash
kill $(lsof -t -i:3333)
```

- [How to kill a process running on particular port in Linux?](https://stackoverflow.com/questions/11583562/how-to-kill-a-process-running-on-particular-port-in-linux)

### Important compile flags CXXFLAGS

-O0
-g3
-ggdb
-gdwarf-2

- [openocd.org | 15 General Commands](https://openocd.org/doc/html/General-Commands.html)
- [openocd.org | 20 GDB and OpenOCD](https://openocd.org/doc/html/GDB-and-OpenOCD.html)

[Top](#table-of-contents)
## External references
- [Open On-Chip Debugger](https://openocd.org/pages/getting-openocd.html)
- [youtube.com | Low Level Learning | This Is 100% How You Should Be Debugging | How to Use OpenOCD to Debug Embedded Software with GDB](https://www.youtube.com/watch?v=_1u7IOnivnM)
- [Debugging an embedded ARM device in Visual Studio](https://devblogs.microsoft.com/cppblog/debugging-an-embedded-arm-device-in-visual-studio/)
- [github.com | STMicroelectronics | OpenOCD](https://github.com/STMicroelectronics/OpenOCD)

