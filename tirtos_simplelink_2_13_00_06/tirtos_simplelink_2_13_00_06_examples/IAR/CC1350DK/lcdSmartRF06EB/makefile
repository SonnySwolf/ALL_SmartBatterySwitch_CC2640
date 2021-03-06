include ../makedefs

###### recursive wildcard function #######
rwildcard=$(wildcard $1$2) $(foreach d, $(wildcard $1*),$(call rwildcard,$d/,$2))

###### returns all sources in examples directory ######
SOURCES   = $(wildcard *.c)
ASM_FILES = $(wildcard *.asm)

###### returns all directories except one generated by configuro ######
DIRS = $(filter-out $(NAME)/, $(wildcard */))

###### recursively find all sources in every returned directory ######
SOURCES   += $(foreach d, $(DIRS),$(call rwildcard,$(d),*.c))
ASM_FILES += $(foreach d, $(DIRS),$(call rwildcard,$(d),*.asm))


###### return list of .obj's from all .c's ######
OBJECTS  = $(patsubst %.c, %.obj, $(SOURCES))
OBJECTS += $(patsubst %.asm, %.obj, $(ASM_FILES))

###### return config file name #######
NAME = $(strip $(patsubst %.cfg, %, $(wildcard *.cfg)))

###### return example name #######
EXAMPLENAME = $(notdir $(shell $(pwd)))

###### Extend compiler options here ######

CFLAGS += 

###### Extend linker options here ######
LFLAGS +=

.PRECIOUS: %/compiler.opt %/linker.cmd

all: $(EXAMPLENAME).out

%/compiler.opt: %/linker.cmd ;

%.obj : %.c
%.obj : %.asm

%/linker.cmd: %.cfg
	@ echo Running Configuro...
	@ $(XDCTOOLS_INSTALLATION_DIR)/xs xdc.tools.configuro -c "$(CODEGEN_INSTALLATION_DIR)" -t $(XDCTARGET) -p $(PLATFORM) --compileOptions "$(CFLAGS)" $(NAME).cfg

%.obj: %.c $(NAME)/compiler.opt
	@ echo Building $@
	@ $(CC)  $(CFLAGS) $< -f $(NAME)/compiler.opt -o $@

%.obj: %.asm $(NAME)/compiler.opt
	@ echo Building $@
	@ $(CC)  $(CFLAGS) $< -f $(NAME)/compiler.opt -o $@

$(EXAMPLENAME).out: $(OBJECTS) $(NAME)/linker.cmd
	@ echo linking...
	@ $(LNK)  $(OBJECTS) -f $(NAME)/linker.cmd $(LFLAGS) -o $(EXAMPLENAME).out

clean:
	@ echo Cleaning...
	@ $(call remove, $(OBJECTS))
	@ $(call remove, $(EXAMPLENAME).out)
	@ $(call remove, $(NAME).map)
	@ $(RMDIR) $(NAME)
