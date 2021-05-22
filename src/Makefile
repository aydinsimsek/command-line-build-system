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

#------------------------------------------------------------------------------------------------------------
# This file is a makefile that supports the target embedded system(MSP432) and the host environment(Linux) 
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.i: Preprocessed output   
#      <FILE>.asm: Assembly output 
#      <FILE>.o: Object file
#      compile-all: Compile all object files 
#      build: Compile all object files and link into a final executable  
#      clean: Remove all compiled objects, preprocessed outputs, assembly outputs, executable files and 
#             build output files 
#
# Platform Overrides:
#      MSP432
#      HOST 
#
# @author Aydin Simsek 
# @date 05/22/2021 
#------------------------------------------------------------------------------------------------------------

include sources.mk

# Platform Overrides
PLATFORM = HOST

TARGET = c1m2

ifeq ($(PLATFORM), MSP432)
	# Architectures Specific Flags
	LINKER_FILE = ../msp432p401r.lds 
	CPU = cortex-m4
	ARCH = armv7e-m
	SPECS = nosys.specs
	
	# Compiler Flags and Defines
	CC = arm-none-eabi-gcc
	LD = arm-none-eabi-ld
	CFLAGS = -Wall -Werror -g -O0 -std=c99 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -mcpu=$(CPU) -march=$(ARCH) --specs=$(SPECS)
	LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
	CPPFLAGS = -DMSP432 $(INCLUDES)
	SIZE = arm-none-eabi-size

else
	# Complier Flags and Defines
	CC = gcc
	CFLAGS = -Wall -Werror -g -O0 -std=c99
	CPPFLAGS = -DHOST $(INCLUDES)
	LDFLAGS = -Wl,-Map=$(TARGET).map
	SIZE = size
endif

OBJS = $(SOURCES:.c=.o)
DEPS = $(SOURCES:.c=.d)
ASMS = $(SOURCES:.c=.asm)
PREP = $(SOURCES:.c=.i)

%.o: %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -o $@

%.asm: %.c
	$(CC) -S $< $(CFLAGS) $(CPPFLAGS) -o $@

%.i: %.c
	$(CC) -E $< $(CPPFLAGS) -o $@

%.d: %.c
	$(CC) -E -M $< $(CPPFLAGS) -o $@

.PHONY: compile-all
compile-all: $(OBJS)

.PHONY: Build
build: $(TARGET).out

$(TARGET).out: $(OBJS) $(DEPS) 
	$(CC) $(OBJS) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) -o $@
	$(SIZE) $@

.PHONY: Clean
clean:
	rm -f *.o *.out *.asm *.d *.i *.map 
