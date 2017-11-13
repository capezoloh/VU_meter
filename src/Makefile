MCU=atmega328p
F_CPU=20000000
PROG=usbasp


TARGET=main

C_SRC= \
$(TARGET).c \
fault.c \
ring_buffer.c \
i2c.c \
background.c \
peak_indicator.c \
drawing.c
# ssd1306.c

C_OBJS=$(C_SRC:.c=.o)
OBJS=$(C_OBJS)


CC=avr-gcc
CXX=avr-g++
LD=avr-ld
AR=avr-ar
AS=avr-gcc -c
OBJCOPY=avr-objcopy
OBJDUMP=avr-objdump
SIZE=avr-size
RM=rm -f --
IMAGE2C=python3 image2c.py


CFLAGS+=-mmcu=$(MCU)
CFLAGS+=-DF_CPU="$(F_CPU)UL"

CFLAGS+=-O1
CFLAGS+=-MD -MP
CFLAGS+=-Wall
CFLAGS+=-Werror
CFLAGS+=-std=c99
CFLAGS+=-g
#CFLAGS+=-DNDEBUG
CFLAGS+=-ffunction-sections -fdata-sections
CFLAGS+=-funsigned-char -funsigned-bitfields
CFLAGS+=-fpack-struct -fshort-enums
#CFLAGS+=-mshort-calls
CFLAGS+=-frename-registers
#CFLAGS+=-fverbose-asm
CFLAGS+=-Wa,-ahlmsd=$(<:.c=.lst)
# -a[cdghlmns]
#		 Turn on listings, in any of a variety of ways:
#		 -ac omit false conditionals
#		 -ad omit debugging directives
#		 -ag include general information, like as version and options passed
#		 -ah include high-level source
#		 -al include assembly
#		 -am include macro expansions
#		 -an omit forms processing
#		 -as include symbols
#		 =file
#				 set the name of the listing file



LDFLAGS+=-mmcu=$(MCU)
LDFLAGS+=-static
LDFLAGS+=-Wl,-Map=map.map,--cref
LDFLAGS+=-Wl,-gc-sections
# Minimalna wersja printf
#LDFLAGS += -Wl,-u,vfprintf -lprintf_min
# Zmiennoprzecinkowa wersja printf (wymaga biblioteki matematycznej)
#LDFLAGS += -Wl,-u,vfprintf -lprintf_flt
#LDFLAGS+=-g
LDLIBS+=-lm


ASFLAGS+=-mmcu=$(MCU)
ASFLAGS+=-DF_CPU="$(F_CPU)UL"
ASFLAGS+=-x assembler-with-cpp
ASFLAGS+=-Wa,-ahlms=$(<:.s=.lst),-g,--gstabs


all: $(TARGET).hex

background.c: images/background.png
	$(IMAGE2C) $< $@ BACKGROUND

peak_indicator.c: images/peak_indicator.png
	$(IMAGE2C) $< $@ PEAK_INDICATOR

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) $(TARGET_ARCH) $^ $(LOADLIBES) $(LDLIBS) -o $@

%.hex: %
	$(OBJCOPY) -O ihex -R .eeprom $< $@

%.o: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) $(TARGET_ARCH) -c $< -o $@


summary: $(TARGET)
	nm --print-size --size-sort --radix=d main
	$(SIZE) $(TARGET)


install: $(TARGET)
	avrdude -p $(MCU) -c $(PROG) -U flash:w:$(TARGET) #-U eeprom:w:$(TARGET).eep


clean:
	$(RM) $(TARGET)
	$(RM) $(OBJS)
	$(RM) $(OBJS:.o=.map)
	$(RM) $(OBJS:.o=.d)
	$(RM) $(OBJS:.o=.lst)
	$(RM) map.map
	$(RM) $(TARGET).hex
	$(RM) $(TARGET).eep
	$(RM) background.c
	$(RM) peak_indicator.c

.PHONY: all summary install clean
-include $(C_SRC:.c=.d)