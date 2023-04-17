LIBRARY_INCLUDES := -I ./lib/argparse/include/ \
					-I ./lib/tomlplusplus/include/

LXX_FLAGS :=

CXX := g++
CXX_FLAGS := -std=c++23 \
			$(LIBRARY_INCLUDES) \
			$(LXX_FLAGS)

FORMATTER := clang-format

SOURCE_DIRECTORY := ./src
TARGET_DIRECTORY := ./dist

##############################################################################

NULLFILE := /dev/null
CPP_FILES := $(shell find $(SOURCE_DIRECTORY) -regextype egrep \
			 -regex ".*\.(cc|cp|cxx|cpp|cPP|c\+\+|C)$$")
CPP_HEADER_FILES := $(shell find $(SOURCE_DIRECTORY) -regextype egrep \
				-regex ".*\.(hh|H|hp|hxx|hpp|HPP|h++|tcc)")
SOURCE_FILES := $(CPP_HEADER_FILES) $(CPP_FILES)
OBJECT_FILES := $(patsubst $(SOURCE_DIRECTORY)/%.cpp, $(TARGET_DIRECTORY)/objects/%.o, $(CPP_FILES))
SOURCE_DIRECTORY_STRUCTURE := $(shell find $(SOURCE_DIRECTORY) -type d)
DIST_DIRECTORY_STRUCTURE := $(patsubst $(SOURCE_DIRECTORY)/%,$(TARGET_DIRECTORY)/objects/%, \
								$(filter-out $(SOURCE_DIRECTORY),$(SOURCE_DIRECTORY_STRUCTURE)))
BIN_DIST_DIRECTORY_STRUCTURE := $(patsubst $(SOURCE_DIRECTORY)%, $(TARGET_DIRECTORY)/bin/%, \
									$(filter-out $(SOURCE_DIRECTORY),$(SOURCE_DIRECTORY_STRUCTURE)))
POSSIBLE_PROGRAM_NAMES := $(basename $(patsubst $(SOURCE_DIRECTORY)/%, %, \
						  $(filter-out $(SOURCE_DIRECTORY),$(CPP_FILES))))

define simplify_path # removes './' from the beginning of the path and removes any '/./' from the path
$(patsubst ./%,%,$(subst /./,/,$(subst /./,/, $(patsubst %/,%, $(1)))))
endef

define OBJ_HAS_MAIN_FUNCTION_MATCHER # adds '_not_main' to the end of the object file name if it does not contain a main function
$(shell nm $(1) 2> $(NULLFILE) | grep -E "[[:xdigit:]]\sT\smain" > $(NULLFILE) && echo "$(1)_yes_main" || echo "$(1)_not_main")
endef

define _UNMATCHED_OBJECT_FILES
$(foreach obj_file,$(OBJECT_FILES),$(call OBJ_HAS_MAIN_FUNCTION_MATCHER,$(obj_file)))
endef

define NON_MAIN_OBJECT_FILES
$(call simplify_path,$(patsubst %_not_main, %, $(filter %_not_main,$(_UNMATCHED_OBJECT_FILES))))
endef

define MAIN_OBJECT_FILES
$(call simplify_path,$(patsubst %_yes_main, %, $(filter %_yes_main,$(_UNMATCHED_OBJECT_FILES))))
endef

.PHONY: clean print dist_dirs all $(POSSIBLE_PROGRAM_NAMES) format

all: $(OBJECT_FILES) | dist_dirs

format:
	$(FORMATTER) -i $(SOURCE_FILES)

$(POSSIBLE_PROGRAM_NAMES): %: $(TARGET_DIRECTORY)/bin/% | dist_dirs

$(TARGET_DIRECTORY)/bin/%: $(TARGET_DIRECTORY)/objects/%.o $(OBJECT_FILES) | dist_dirs
	$(CXX) $(CXX_FLAGS) $(NON_MAIN_OBJECT_FILES) $< -o $@

$(TARGET_DIRECTORY)/objects/%.o: ./$(SOURCE_DIRECTORY)/%.cpp $(CPP_HEADER_FILES) | dist_dirs
	$(CXX) $(CXX_FLAGS) -c $< -o $@

clean:
	@[ -e $(TARGET_DIRECTORY) ] && rm -r $(TARGET_DIRECTORY) || :
	@[ "$(shell find -type l -name 'bin')" == "./bin" ] && rm ./bin || :

dist_dirs:
	@mkdir -p $(TARGET_DIRECTORY)/objects
	@mkdir -p $(TARGET_DIRECTORY)/bin/$(BIN_DIST_DIRECTORY_STRUCTURE)
	@mkdir -p $(DIST_DIRECTORY_STRUCTURE)
	@ln -fs $(TARGET_DIRECTORY)/bin .

print: # Tests for wildcards
	@echo "SOURCE_DIRECTORY: $(SOURCE_DIRECTORY)"
	@echo "TARGET_DIRECTORY: $(TARGET_DIRECTORY)"

	@echo "CPP_FILES: $(CPP_FILES)"
	@echo "CPP_HEADER_FILES: $(CPP_HEADER_FILES)"
	@echo "OBJECT_FILES: $(OBJECT_FILES)"
	@echo "SOURCE_FILES: $(SOURCE_FILES)"
	@echo "SOURCE_DIRECTORY_STRUCTURE: $(SOURCE_DIRECTORY_STRUCTURE)"
	@echo "DIST_DIRECTORY_STRUCTURE: $(DIST_DIRECTORY_STRUCTURE)"
	@echo "NON_MAIN_OBJECT_FILES: $(NON_MAIN_OBJECT_FILES)"
	@echo "MAIN_OBJECT_FILES: $(MAIN_OBJECT_FILES)"
	@echo "POSSIBLE_PROGRAM_NAMES: $(POSSIBLE_PROGRAM_NAMES)"
