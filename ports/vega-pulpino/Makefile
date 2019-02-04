include ../../py/mkenv.mk

# Point this to Vega-SDK
VEGA_SDK ?= /opt/Vega-SDK

# qstr definitions (must come before including py.mk)
QSTR_DEFS = qstrdefsport.h

# include py core make definitions
include $(TOP)/py/py.mk

CROSS_COMPILE = /opt/pulp/bin/riscv32-unknown-elf-

INC += -I.
INC += -I$(TOP)
INC += -I$(BUILD)
INC += -I$(VEGA_SDK)/RISCV
INC += -I$(VEGA_SDK)/devices
INC += -I$(VEGA_SDK)/devices/RV32M1
INC += -I$(VEGA_SDK)/devices/RV32M1/drivers
INC += -I$(VEGA_SDK)/devices/RV32M1/utilities

ASM_SRCS += $(VEGA_SDK)/devices/RV32M1/gcc/startup_RV32M1_ri5cy.S 

DBFLAGS = -DNDEBUG -DCPU_RV32M1_ri5cy -DPRINTF_FLOAT_ENABLE=0 -DSCANF_FLOAT_ENABLE=0 -DPRINTF_ADVANCED_ENABLE=0 -DSCANF_ADVANCED_ENABLE=0
CFLAGS = $(INC) $(DBFLAGS) -O1 -msmall-data-limit=8 -mno-save-restore -flto -fmessage-length=0 -fsigned-char -fno-common -ffreestanding -fno-builtin -fno-strict-aliasing -std=gnu99 -march=rv32imcxpulpv2 -Wa,-march=rv32imcxpulpv2
LDFLAGS = -T RV32M1_ri5cy_flash.ld -nostdlib -flto -O1 -Wl,-Map,micropython.map -march=rv32imcxpulpv2 -Xlinker -static -Xlinker -z -Xlinker muldefs -Xlinker -static -Xlinker -z -Xlinker muldefs
LIBS = -lnosys -lgcc -lc -lm -lm -lg -lgcc -lnosys -Xlinker --end-group 

SRC_C = \
	main.c \
	board.c \
	pin_mux.c \
	clock_config.c \
	$(VEGA_SDK)/devices/RV32M1/drivers/fsl_common.c \
	$(VEGA_SDK)/devices/RV32M1/system_RV32M1_ri5cy.c \
	$(VEGA_SDK)/devices/RV32M1/drivers/fsl_msmc.c \
	$(VEGA_SDK)/devices/RV32M1/utilities/fsl_str.c \
	$(VEGA_SDK)/devices/RV32M1/utilities/fsl_io.c \
	$(VEGA_SDK)/devices/RV32M1/drivers/fsl_clock.c \
	$(VEGA_SDK)/devices/RV32M1/utilities/fsl_log.c \
	$(VEGA_SDK)/devices/RV32M1/utilities/fsl_debug_console.c \
	$(VEGA_SDK)/devices/RV32M1/drivers/fsl_gpio.c \
	$(VEGA_SDK)/devices/RV32M1/drivers/fsl_lpuart.c \
	lib/utils/stdout_helpers.c \
	lib/utils/pyexec.c \
	lib/mp-readline/readline.c

OBJ = $(PY_O) $(addprefix $(BUILD)/, $(SRC_C:.c=.o)) $(addprefix $(BUILD)/, $(ASM_SRCS:.S=.o))

all: micropython

micropython: $(OBJ)
	$(ECHO) "LINK $@"
	$(Q)$(CC) $(LDFLAGS) -o $@.elf -Xlinker --start-group $(OBJ) $(LIBS)

# List of sources for qstr extraction
SRC_QSTR += $(SRC_C) mpconfigport.h
# Append any auto-generated sources that are needed by sources listed in
# SRC_QSTR
SRC_QSTR_AUTO_DEPS +=

include $(TOP)/py/mkrules.mk