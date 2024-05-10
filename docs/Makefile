PROGRAM=bare
OBJECTS=bare.o system_stm32f4xx.o startup_stm32f429zitx.s

TARGET_FLAGS=\
	--specs=nosys.specs\
	-mcpu=cortex-m4\
	-mthumb\
	-mlittle-endian\
	-mfpu=fpv4-sp-d16\
	-mfloat-abi=hard\
	-mthumb-interwork

CFLAGS=\
	-g -Wall -Wextra - Werrors\
	$(TARGET_FLAGS)\
	-ICMSIS
	-DSTM32F429xx\
	-DHSE_VALUE=8000000