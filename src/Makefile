ARCH ?= amd64

ARCHS32 := i386
ARCHS64 := amd64 aarch64
ARCHS := $(ARCHS32) $(ARCHS64)
CC = gcc
CFLAGS += -pipe -Wall -Wextra -fPIC -fno-ident -fno-stack-protector -U _FORTIFY_SOURCE
LDFLAGS += -nostartfiles -nodefaultlibs -nostdlib 
LDFLAGS += -pie -e z_start -Wl,-Bsymbolic,--no-undefined,--build-id=none
TARGET := loader
BIN_DIR := ../bin
OBJ_DIR := ../obj

ifeq "$(filter $(ARCH),$(ARCHS))" ""
  $(error ARCH='$(ARCH)' is not supported)
endif

ifeq "$(filter $(ARCH),$(ARCHS32))" "$(ARCH)"
  CFLAGS += -m32 -DELFCLASS=ELFCLASS32
  ASFLAGS += -m32
  LDFLAGS += -m32
else
  CFLAGS += -DELFCLASS=ELFCLASS64
endif

CFLAGS += -O0 -g
ASFLAGS += -g
# ifdef DEBUG
#   CFLAGS += -O0 -g
#   ASFLAGS += -g
# else
#   CFLAGS += -fvisibility=hidden
#   #Disable unwind info to make prog smaller.
#   CFLAGS += -Os -fno-asynchronous-unwind-tables -fno-unwind-tables
#   LDFLAGS += -s
# endif

# Create OBJ_DIR and its subdirectories structure
OBJS := $(patsubst %.c,$(OBJ_DIR)/%.o, $(wildcard *.c))
OBJS += $(patsubst %.S,$(OBJ_DIR)/%.o, $(wildcard $(ARCH)/*.S))

ifdef SMALL
  OBJS := $(filter-out $(OBJ_DIR)/z_printf.%,$(OBJS))
  OBJS := $(filter-out $(OBJ_DIR)/z_err.%,$(OBJS))
  CFLAGS += -DZ_SMALL
endif

# Automatically create directories for object files
$(OBJ_DIR)/%.o: %.c
	@mkdir -p $(@D)
	$(CC) $(CFLAGS) -c $< -o $@

$(OBJ_DIR)/%.o: %.S
	@mkdir -p $(@D)
	$(CC) $(ASFLAGS) -c $< -o $@

.PHONY: clean all

all: $(BIN_DIR)/$(TARGET)

$(BIN_DIR)/$(TARGET): $(OBJS)
	@mkdir -p $(BIN_DIR)
	$(CC) $(LDFLAGS) -o $@ $^

clean:
	rm -rf $(OBJ_DIR) $(BIN_DIR)
