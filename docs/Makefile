PROGRAM=bare
OBJECTS=main.o system_stm32f4xx.o startup_stm32f429zitx.s

TARGET_FLAGS=\
	--specs=nosys.specs\
	-mcpu=cortex-m4\
	-mthumb\
	-mlittle-endian\
	-mfpu=fpv4-sp-d16\
	-mfloat-abi=hard\
	-mthumb-interwork

CFLAGS=\
	-g -Wall -Wextra - Werror\
	$(TARGET_FLAGS)\
	-ICMSIS
	-DSTM32F429xx\
	-DHSE_VALUE=8000000

LDFLAGS=\
	$(TARGET_FLAGS)\
	-wl, -Tlinker.ld

%.o : %.c
  arm-none-eabi-gcc $(CFLAGS) -c -o $@ $<

$(PROGRAM) : $(OBJECTS)
  arm-none-eabi-gcc $(LDFLAGS) -o $@ $(OBJECTS)

flash:
  openocd -f /usr/share/openocd/scripts/interface/stlink.cfg\
 -f /usr/share/openocd/scripts/target/stm32f4x.cfg

debug:
  to be completed

clean:
  rm *.o $(PROGRAM)

.PHONY: flash debug clean