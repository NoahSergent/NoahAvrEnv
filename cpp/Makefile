# Config

MCU			= atmega328p
F_CPU		= 16000000UL
CXXSTD		= -std=c++20
OPT			= -Os
TARGET		= firmware

SRC_DIR 	= src
INCLUDE_DIR	= src/include
BUILD_DIR 	= build
OBJ_DIR		= $(BUILD_DIR)/obj
BIN_DIR 	= $(BUILD_DIR)/bin

SOURCES 	= $(wildcard $(SRC_DIR)/*.cpp) $(wildcard $(INCLUDE_DIR)/*.cpp)
OBJECTS 	= $(patsubst $(SRC_DIR)/%.cpp, $(OBJ_DIR)/%.o,$(SOURCES))


TOOLCHAIN 	= Toolchain/avr8-gnu-toolchain-win32_x86_64/bin/
AVRDUDE		= C:/WinAVR-20100110/bin/avrdude.exe
AVRDUDE_MCU = m328p
PROGRAMMER  = usbtiny

CC 			= $(TOOLCHAIN)avr-gcc
CXX 		= $(TOOLCHAIN)avr-g++
OBJCOPY		= $(TOOLCHAIN)avr-objcopy
SIZE 		= $(TOOLCHAIN)avr-size

CDEFS		= -DF_CPU=$(F_CPU)
CINCLUDES	= -I$(SRC_DIR) -I$(INCLUDE_DIR)
CWARN		= -Wall -Wextra -Wpedantic

CFLAGS		= -mmcu=$(MCU) $(OPT) $(CDEFS) $(CINCLUDES) $(CWARN)
CXXFLAGS 	= $(CFLAGS) $(CXXSTD) -fno-exceptions -fno-rtti
LDFLAGS		= -mmcu=$(MCU)

ELF			= $(BIN_DIR)/$(TARGET).elf
HEX			= $(BIN_DIR)/$(TARGET).hex

# TODO: ADD FLASH CMD

# RULES
all: dirs $(HEX) size

dirs:
	@mkdir -p $(OBJ_DIR) $(BIN_DIR)

$(OBJ_DIR)/%.o: $(SRC_DIR)/%.cpp
	@mkdir -p $(dir $@)
	$(CXX) $(CXXFLAGS) -c $< -o $@

$(ELF): $(OBJECTS)
	$(CXX) $(LDFLAGS) $^ -o $@

$(HEX): $(ELF)
	$(OBJCOPY) -O ihex -R .eeprom $< $@

size: $(ELF)
	$(SIZE) $(ELF)

clean:
	@rm -rf $(BUILD_DIR)

flash: $(HEX)
	$(AVRDUDE) -p $(AVRDUDE_MCU) -c $(PROGRAMMER) -v -v -v -U flash:w:$(HEX):i

# For USBasp (no -P/-b typically):
# flash: $(HEX)
# 	$(AVRDUDE) -p $(MCU) -c $(PROGRAMMER) -D -U flash:w:$(HEX):i

.PHONY: all clean flash size dirs