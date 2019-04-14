#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# Coursera m2 - Compilation with GCC and GNU Make
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <Put a description of the supported targets here>
#
# Platform Overrides:
#      <Put a description of the supported Overrides here
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
ifeq ($(PLATFORM),HOST)
	CC = $(NATIVECC)
	PLATFORMFLAGS = $(HOSTFLAG)
	LINKER_FILE = 
else
	CC = $(CROSSCC)
	PLATFORMFLAGS = $(MSP432FLAGS)
	LINKER_FILE = -T ../msp432p401r.lds
endif

# Architectures Specific Flags
CPU = cortex-m4 
ARCH = thumb
VER = armv7e-m
TYPE = hard
FPU = fpv4-sp-d16
SPECS = nosys.specs

# Compiler Flags and Defines
NATIVECC = gcc
CROSSCC = arm-none-eabi-gcc
TARGET = c1m2
LDFLAGS = -Wl,-Map=$(TARGET).map $(LINKER_FILE) 
CFLAGS = -Wall -Werror -g -O0 -std=c99 
HOSTFLAG = -DHOST
MSP432FLAGS = $(ARCHFLAGS) -DMSP432
CPPFLAGS = $(INCLUDE) $(PLATFORMFLAGS)
ARCHFLAGS = -mcpu=$(CPU) -m$(ARCH) -march=$(VER) -mfloat-abi=$(TYPE) -mfpu=$(FPU) --specs=$(SPECS) 

OBJSPREPROC = $(SOURCES:.c=.i)
OBJSASM = $(SOURCES:.c=.asm)
OBJS = $(SOURCES:.c=.o)
TARGETASM = $(TARGET).asm
%.o : %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -o $@

%.i : %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -E -o $@

%.asm : %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -S -o $@

%.asm : %.out 
	objdump -D $< > $@

.PHONY: compile-all
compile-all: $(OBJS) 

.PHONY: build
build: $(TARGET).out

$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) -o $@

.PHONY: clean
clean:
	rm -f $(OBJS) $(OBJSASM) $(OBJSPREPROC) $(TARGET).out $(TARGET).map $(TARGETASM)
