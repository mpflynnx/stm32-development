## libopencm3

## Table of Contents
- [Installation on Ubuntu based distros](#installation-on-ubuntu-based-distros)
- [External references](#external-references)

The `libopencm3` project aims to create an open-source firmware library for various ARM Cortex-M microcontrollers.

### Installation on Ubuntu based distros

libopencm3 needs compiling once for the project to use it, therefore an `arm-none-eabi/arm-elf` toolchain is required, `arm-none-eabi` is the default in Makefile.

```bash
$ sudo apt install gcc-arm-none-eabi
```

### Using libopencm3
A recommended usage of `libopencm3` is having it as a `git submodule` inside your own project. `libopencm3` is still under heavy development and the api may change. It is good to have your project bound to the version of `libopencm3` you know is working.

```bash
git submodule add git://github.com/libopencm3/libopencm3.git externals/libopencm3
git submodule update --init --recursive
cd externals/libopencm3
// Reduce the build time by specifying a particular MCU series
make TARGETS='stm32/f4'
```

[Top](#table-of-contents)
## External references
- [libopencm3 | API Documentation | STM32F4](https://libopencm3.org/docs/latest/stm32f4/html/modules.html)
- [github.com | libopencm3](https://github.com/libopencm3/libopencm3)
- [youtube.com | Low Level Learning | The Best Board to Start STM32 Programming? | ARM Development for Beginners](https://www.youtube.com/watch?v=YEGKD6JQJyM)
- [stackoverflow.com | How to do git submodules and why to use git submodules](https://stackoverflow.com/questions/31790481/how-to-do-git-submodules-and-why-to-use-git-submodules)