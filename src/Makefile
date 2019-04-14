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
#      <FILE>.i - Generates the preprocessed output of all c-program
#      implemention files.
#      
#      <FILE>.asm - Generates assembly output of c-program implementation files
#      and the final output executable
#      
#      <FILE>.o - Generates the object file for all c-source files (but do not
#      link) by specifying the object file you want to compile
#      
#      compile-all - Compile all object files (NO LINKING w/ .PHONY protection)
#      
#      build - Compile all object files and link into a final executable
#      (w/ .PHONY protection)
#      
#      clean - Removes all compiled objects, preprocessed outputs, assembly
#      outputs, executable files and build output files (w/ .PHONY protection)
#      
# Platform Overrides:
#      PLATFORM - Support for two target platforms (HOST, MSP432)
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
ifeq ($(PLATFORM),HOST)
	CC = $(NATIVECC)
	PLATFORMFLAGS = $(HOSTFLAG)
	OBJDUMP = $(ODUMP)
else
	CC = $(CROSSCC)
	PLATFORMFLAGS = $(MSP432FLAGS)
	LINKER_FILE = -T ../msp432p401r.lds
	OBJDUMP = arm-none-eabi-$(ODUMP)
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
ODUMP = objdump
TARGET = c1m2
LDFLAGS = -Wl,-Map=$(TARGET).map $(LINKER_FILE) 
CFLAGS = -Wall -Werror -g -O0 -std=c99 
HOSTFLAG = -DHOST
MSP432FLAGS = $(ARCHFLAGS) -DMSP432
CPPFLAGS = $(INCLUDE) $(PLATFORMFLAGS)
ARCHFLAGS = -mcpu=$(CPU) -m$(ARCH) -march=$(VER) -mfloat-abi=$(TYPE) -mfpu=$(FPU) --specs=$(SPECS) 

# Output Files Defines
OBJSPREPROC = $(SOURCES:.c=.i)
OBJSASM = $(SOURCES:.c=.asm)
OBJDEP = $(SOURCES:.c=.d)
OBJSIZE = $(SOURCES:.c=.size)
OBJS = $(SOURCES:.c=.o)
TARGETASM = $(TARGET).asm

# Pattern Matching Rules to Generate Individual Files

%.o : %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -MD -o $@
	size -d $@ > $*.size

%.i : %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -E -o $@

%.asm : %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -S -o $@

%.asm : %.out 
	$(OBJDUMP) -D $< > $@

.PHONY: compile-all
compile-all: $(OBJS) 

.PHONY: build
build: $(TARGET).out

$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) -o $@
	size -d $(TARGET).out > $(TARGET).size
.PHONY: clean
clean:
	rm -f $(OBJS) $(OBJSIZE) $(OBJDEP) $(OBJSASM) $(OBJSPREPROC) $(TARGET).out $(TARGET).map $(TARGETASM) $(TARGET).size
