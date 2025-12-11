# Config

MCU			= atmega328p
F_CPU		= 16000000UL
CXXSTD		= -std=c++20
OPT			= -Os
TARGET		= firmware
S_TARGET  	= scoreboard


SRC_DIR 	= Mechatronics_Final
SCORE_DIR   = Mechatronics_Final/scoreboard_code
INCLUDE_DIR	= src/include
BUILD_DIR 	= build
OBJ_DIR		= $(BUILD_DIR)/obj
BIN_DIR 	= $(BUILD_DIR)/bin

SOURCES 	= $(wildcard $(SRC_DIR)/*.cpp) $(wildcard $(INCLUDE_DIR)/*.cpp)
OBJECTS 	= $(patsubst $(SRC_DIR)/%.cpp, $(OBJ_DIR)/%.o,$(SOURCES))

SCORE_SOURCES = $(wildcard $(SCORE_DIR)/*.cpp)
SCORE_OBJECTS = $(patsubst $(SCORE_DIR)/%.cpp, $(OBJ_DIR)/%.o,$(SCORE_SOURCES))


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

S_CINCLUDES	= -I$(SCORE_DIR)
S_CFLAGS	= -mmcu=$(MCU) $(OPT) $(CDEFS) $(S_CINCLUDES) $(CWARN)
S_CXXFLAGS 	= $(CFLAGS) $(CXXSTD) -fno-exceptions -fno-rtti

ELF			= $(BIN_DIR)/$(TARGET).elf
HEX			= $(BIN_DIR)/$(TARGET).hex

SCORE_ELF			= $(BIN_DIR)/$(S_TARGET).elf
SCORE_HEX			= $(BIN_DIR)/$(S_TARGET).hex


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

flash-s: $(HEX)
	$(AVRDUDE) -p $(AVRDUDE_MCU) -c $(PROGRAMMER) -v -v -v -U flash:w:$(SCORE_HEX):i

score: score-dirs $(SCORE_HEX) score-size

score-dirs:
	@mkdir -p $(OBJ_DIR) $(BIN_DIR)

$(OBJ_DIR)/%.o: $(SCORE_DIR)/%.cpp
	@mkdir -p $(dir $@)
	$(CXX) $(S_CXXFLAGS) -c $< -o $@

$(SCORE_ELF): $(OBJECTS)
	$(CXX) $(LDFLAGS) $^ -o $@

$(SCORE_HEX): $(SCORE_ELF)
	$(OBJCOPY) -O ihex -R .eeprom $< $@

score-size: $(SCORE_ELF)
	$(SIZE) $(SCORE_ELF)


# For USBasp (no -P/-b typically):
# flash: $(HEX)
# 	$(AVRDUDE) -p $(MCU) -c $(PROGRAMMER) -D -U flash:w:$(HEX):i

.PHONY: all clean flash size dirs