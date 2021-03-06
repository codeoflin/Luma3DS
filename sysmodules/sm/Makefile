rwildcard = $(foreach d, $(wildcard $1*), $(filter $(subst *, %, $2), $d) $(call rwildcard, $d/, $2))

ifeq ($(strip $(DEVKITARM)),)
$(error "Please set DEVKITARM in your environment. export DEVKITARM=<path to>devkitARM")
endif

include $(DEVKITARM)/3ds_rules

name := sm

dir_source := source
dir_build := build
dir_out := ../../$(dir_build)

LIBS := -lctru
LIBDIRS	:= $(CTRULIB)
LIBPATHS := $(foreach dir,$(LIBDIRS),-L$(dir)/lib)

INCLUDE	:= $(foreach dir,$(LIBDIRS),-I$(dir)/include)

ARCH := -mcpu=mpcore -mfloat-abi=hard -mtp=soft
CFLAGS := -Wall -Wextra -MMD -MP -marm $(ARCH) -fno-builtin -std=c11 -O2 -g -ffast-math -mword-relocations \
	  -ffunction-sections -fdata-sections -fno-strict-aliasing $(INCLUDE) -DARM11 -D_3DS
LDFLAGS := -specs=3dsx.specs -Wl,--gc-sections $(ARCH)

objects = $(patsubst $(dir_source)/%.c, $(dir_build)/%.o, \
          $(call rwildcard, $(dir_source), *.c))

.PHONY: all
all: $(dir_out)/$(name).cxi

.PHONY: clean
clean:
	@rm -rf $(dir_build)

$(dir_out)/$(name).cxi: $(dir_build)/$(name).elf
	@makerom -f ncch -rsf $(name).rsf -nocodepadding -o $@ -elf $<

$(dir_build)/$(name).elf: $(objects)
	$(LINK.o) $(OUTPUT_OPTION) $^ $(LIBPATHS) $(LIBS)

$(dir_build)/memory.o : CFLAGS += -O3

$(dir_build)/%.o: $(dir_source)/%.c
	@mkdir -p "$(@D)"
	$(COMPILE.c) $(OUTPUT_OPTION) $<
include $(call rwildcard, $(dir_build), *.d)
