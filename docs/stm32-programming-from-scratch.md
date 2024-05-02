## STM32 Programming From Scratch | C Programming | Assembler | GCC | Makefile

Notes made while watching [STM32 Programming From Scratch | C Programming | Assembler | GCC | Makefile](https://www.youtube.com/watch?v=gdRmETe4QEo&t) by [Martin K. Schröder](https://www.youtube.com/@mkschreder)

You need to have available the reference manual for the device you want to program.

- [pdf | STM32F429 Reference manual](https://www.st.com/content/ccc/resource/technical/document/reference_manual/3d/6d/5a/66/b4/99/40/d4/DM00031020.pdf/files/DM00031020.pdf/jcr:content/translations/en.DM00031020.pdf)

Create a main.c file that contains just a main function and a infinite while loop.

```c
int main(void) {
    while(1);
    return 0;
}
```

Link program (main.c) to a binary that can run on stm32 mcu.

To create the binary file we need to find the memory map of the device from the reference manual.

Page 71/1756 Table 4. Memory mapping vs. Boot mode/physical remap in STM32F42xxx and STM32F43xxx

The code will reside in FLASH memory.

| Addresses | Boot/Remap in main Flash memory | Boot/Remap in embedded SRAM | Boot/Remap in System memory | Remap in FMC |
| ------- | ------- | ------- | ------- | ------- |
| 0x0800 0000 - 0x081F FFFF | Flash memory | Flash memory | Flash memory | Flash memory |

First word in flash is the pointer to the stack the second is the program counter


Small applications can be run from RAM.

Smaller RAM memory - Core coupled memory CCMRAM

| Addresses | Boot/Remap in main Flash memory | Boot/Remap in embedded SRAM | Boot/Remap in System memory | Remap in FMC |
| ------- | ------- | ------- | ------- | ------- |
| 0x2002 0000 - 0x2002 FFFF | SRAM3 (64 KB) | SRAM3 (64 KB) | SRAM3 (64 KB) | SRAM3 (64 KB) |

Main SRAM memory - RAM for variables 
| Addresses | Boot/Remap in main Flash memory | Boot/Remap in embedded SRAM | Boot/Remap in System memory | Remap in FMC |
| ------- | ------- | ------- | ------- | ------- |
| 0x2001 C000 - 0x2001 FFFF | SRAM2 (16 KB) | SRAM2 (16 KB) | SRAM2 (16 KB)| SRAM2 (16 KB) |
| 0x2000 0000 - 0x2001 BFFF | SRAM1 (112 KB) | SRAM1 (112 KB) | SRAM1 (112 KB) | SRAM1 (112 KB)|

### Linker script description

C code is compiled into object code then the linker, links all object files to create the final binary .elf file.

The linker script tells the linker where to place all the code in memory.

[A simple linker script example](/docs/linker.ld)

The linker script is used to define the memory layout. 
```ld
/* Entry Point */
ENTRY(Reset_Handler)
```

These values come direct from the reference manual for the device.

```ld
/* Memories definition */
MEMORY
{
  FLASH (rx)    : ORIGIN = 0x08000000,   LENGTH = 2048K
  CCMRAM(xrw)   : ORIGIN = 0x10000000,   LENGTH = 64K
  RAM(xrw)      : ORIGIN = 0x20000000,   LENGTH = 192K

}
```

Sometimes 0x08000000 is written 0x8000000. The padding before the 8 is just for formatting reasons.

Calculate location of stack pointer, place it at the end of main SRAM memory. Using _estack symbol.

```ld
/* Highest address of the user mode stack */
_estack = ORIGIN(RAM) + LENGTH(RAM); /* end of "RAM" Ram type memory */
```

This section may not be required if the program doesn't use the a heap
```ld
_Min_Heap_Size = 0x200; /* required amount of heap */
_Min_Stack_Size = 0x400; /* required amount of stack */
```

The next section to define is SECTIONS. Here we define how our code will be placed.

```ld
/* Sections */
SECTIONS
{
```

First we define the interrupt vector. This can be found in the isr table in the reference manual at Table 63. Vector table for STM32F42xxx and STM32F43xxx page 378/1756.

```ld
/* Sections */
SECTIONS
{
  /* The startup code into "FLASH" Rom type memory */
  .isr_vector :
  {
    . = ALIGN(4);
    KEEP(*(.isr_vector)) /* Startup code */
    . = ALIGN(4);
  } >FLASH
```

```ld
} >FLASH
```
The text above in the linker file means that section inside the {} is to go into FLASH.

The .isr_vector section is defined in an assembly .s file normally named startup_*.s or similar, example shown below.

```s
/******************************************************************************
*
* The minimal vector table for a Cortex M3. Note that the proper constructs
* must be placed on this to ensure that it ends up at physical address
* 0x0000.0000.
* 
*******************************************************************************/
   .section  .isr_vector,"a",%progbits
  .type  g_pfnVectors, %object
```

The linker script will find the .isr_vector section in the .s file and include that in the final binary .elf file.

Include in the binary any sections in .s that contain .text, such as 

```s
    .section  .text.Reset_Handler
  .weak  Reset_Handler
  .type  Reset_Handler, %function
Reset_Handler: 
  ldr   sp, =_estack       /* set stack pointer */
```

```ld
/* The program code and other data into "FLASH" Rom type memory */
  .text :
  {
    . = ALIGN(4);
    *(.text)           /* .text sections (code) */
    *(.text*)          /* .text* sections (code) */
  } >FLASH
```

Initial values of variables (initialization values) need to be included in the binary, but will eventually go into FLASH from RAM.

```ld
/* Initialized data sections into "RAM" Ram type memory */
  .data :
  {
    . = ALIGN(4);
    _sdata = .;        /* create a global symbol at data start */
    *(.data)           /* .data sections */
    *(.data*)          /* .data* sections */
    . = ALIGN(4);
    _edata = .;        /* define a global symbol at data end */
  } >RAM AT> FLASH
```
Uninitialised values of variables (uninitialization values) need to be included in the binary, but will eventually go into RAM.

```ld
/* Uninitialized data section into "RAM" Ram type memory */
  . = ALIGN(4);
  .bss :
  {
    /* This is used by the startup in order to initialize the .bss section */
    _sbss = .;         /* define a global symbol at bss start */
    __bss_start__ = _sbss;
    *(.bss)
    *(.bss*)
    *(COMMON)

    . = ALIGN(4);
    _ebss = .;         /* define a global symbol at bss end */
    __bss_end__ = _ebss;
  } >RAM
```



## External References
- [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#tables)
- [safeshare | view video without distraction](https://safeshare.tv/x/ss663206661a1d6#)
- [Example of a linker file | Auto-generated by STM32CubeIDE](/docs/STM32F429ZITX_FLASH.ld)
- [Example of a startup file | Auto-generated by STM32CubeIDE ](/docs/startup_stm32f429zitx.s)