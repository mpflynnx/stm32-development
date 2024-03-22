## libopencm3

## Table of Contents
- [Installation on Ubuntu based distros](#installation-on-ubuntu-based-distros)
- [Using libopencm3](#using-libopencm3)
- [Working with libopencm3-template](#working-with-libopencm3-template)
- [Configure my-project Makefile](#configure-my-project-makefile)
- [Blinky for STM32F4](#blinky-for-stm32f4)
- [Build the project](#build-the-project)
- [Flash the project](#flash-the-project)
- [Debug the project using OpenOCD](#debug-the-project-using-openocd)
- [External references](#external-references)

The `libopencm3` project aims to create an open-source firmware library for various ARM Cortex-M microcontrollers.

### Installation on Ubuntu based distros

libopencm3 needs compiling once for the project to use it, therefore an `arm-none-eabi/arm-elf` toolchain is required, `arm-none-eabi` is the default in libopencm3 Makefile.

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

### Working with libopencm3-template

- [github.com | libopencm3-template](https://github.com/libopencm3/libopencm3-template)

Using the libopencm3 template for a new project. Clone the template into a new-project-name folder.
```bash
git clone --recurse-submodules https://github.com/libopencm3/libopencm3-template.git new-project-name
cd new-project-name
make -C libopencm3 # (Only needed once)
```

#### Configure my-project Makefile

Edit file `./new-project-name/my-project/Makefile` in locations shown below:

```bash
PROJECT = awesomesauce # ( change project name )

SHARED_DIR = ../my-common-code # ( delete this if not required )
CFILES = my-project.c
CFILES += api.c  # ( delete this if not required )
AFILES += api-asm.S  # ( delete this if not required )

# TODO - you will need to edit these two lines to match the device and board!
DEVICE=stm32f429zit6
OOCD_FILE = board/stm32f429discovery.cfg
```

#### Blinky for STM32F4

Edit file `./new-project-name/my-project/my-project.c`

The code below will toggle the green led of the `32F429ZI Discovery` every 1 second.

```c
#include "libopencm3/stm32/f4/rcc.h"
#include "libopencm3/stm32/f4/gpio.h"

#define LED_PORT (GPIOG)
#define LED_PIN (GPIO13)

int main(void) {

	rcc_periph_clock_enable(RCC_GPIOG);

	gpio_mode_setup(LED_PORT, GPIO_MODE_OUTPUT, GPIO_PUPD_NONE, LED_PIN);

	while(1) {

		for (uint32_t i = 0; i < 10000000; i++)
		{
			__asm__("nop");
		}

		gpio_toggle(LED_PORT, LED_PIN);	
	}

	return 0;
}
```

#### Build the project
```bash
make -C my-project
```

#### Flash the project

Flash the project using `st-flash`. `st-flash` is part of the the `stlink-tools` package.

Change to the folder containing the generated `.bin` file.

```bash
cd ./new-project-name/my-project
st-flash --reset write awesomesauce.bin 0x08000000
```

#### Debug the project using OpenOCD
See [OpenOCD](openocd.md)


[Top](#table-of-contents)
## External references
- [libopencm3 | API Documentation | STM32F4](https://libopencm3.org/docs/latest/stm32f4/html/modules.html)
- [github.com | libopencm3](https://github.com/libopencm3/libopencm3)
- [youtube.com | Low Level Learning | The Best Board to Start STM32 Programming? | ARM Development for Beginners](https://www.youtube.com/watch?v=YEGKD6JQJyM)
- [stackoverflow.com | How to do git submodules and why to use git submodules](https://stackoverflow.com/questions/31790481/how-to-do-git-submodules-and-why-to-use-git-submodules)
