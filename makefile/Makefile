LIBRARY_INCLUDES :=

CXX := g++
CXX_FLAGS := -Wall \
			-pedantic \
			-pipe \
			-g \
			-std=c++23 \
			$(LIBRARY_INCLUDES)

PROGRAM_NAME := example
SOURCE_DIRECTORY := ./src
MAIN_FILE := $(SOURCE_DIRECTORY)/main.cpp
TARGET_DIRECTORY := ./dist
TEST_FILE_DIRECTORY := ./tests
TEST_FILE_PREFIX := test_
ALTERNATIVE_MAIN_PREFIX := main_

##############################################################################

MAIN_BASE_NAME = $(notdir $(basename $(MAIN_FILE)))
CPP_FILES := $(filter-out $(MAIN_FILE), \
			 $(shell find $(SOURCE_DIRECTORY) -name "*.cpp") \
			 $(shell find $(SOURCE_DIRECTORY) -name "*.cxx") \
			 $(shell find $(SOURCE_DIRECTORY) -name "*.cc"))
HEADER_FILES := $(shell find $(SOURCE_DIRECTORY) -name "*.hpp") \
				$(shell find $(SOURCE_DIRECTORY) -name "*.h") \
				$(shell find $(SOURCE_DIRECTORY) -name "*.h++") \
				$(shell find $(SOURCE_DIRECTORY) -name "*.hh")
SOURCE_FILES := $(HEADER_FILES) $(CPP_FILES)
OBJECT_FILES := $(patsubst $(SOURCE_DIRECTORY)/%.cpp, $(TARGET_DIRECTORY)/objects/%.o, $(CPP_FILES))
SOURCE_DIRECTORY_STRUCTURE := $(shell find $(SOURCE_DIRECTORY) -type d)
DIST_DIRECTORY_STRUCTURE := $(patsubst $(SOURCE_DIRECTORY)/%, $(TARGET_DIRECTORY)/objects/%, $(filter-out $(SOURCE_DIRECTORY),$(SOURCE_DIRECTORY_STRUCTURE)))

define obj_has_main_function # adds '_not_main' to the end of the object file name if it does not contain a main function
	$(shell nm $(1) | grep -E "[[:xdigit:]]\sT\smain" >> /dev/null && echo "$(1)_yes_main" || echo "$(1)_not_main")
endef

_UNMATCHED_OBJECT_FILES := $(foreach obj_file,$(OBJECT_FILES),$(call obj_has_main_function,$(obj_file)))
NON_MAIN_OBJECT_FILES := $(patsubst %_not_main, %, $(filter %_not_main,$(_UNMATCHED_OBJECT_FILES)))
MAIN_OBJECT_FILES := $(patsubst %_yes_main, %, $(filter %_yes_main,$(_UNMATCHED_OBJECT_FILES)))
MAIN_OBJECT_FILES_BASE_NAME := $(notdir $(basename $(MAIN_OBJECT_FILES)))

.PHONY: clean print $(PROGRAM_NAME) dist_dirs

$(PROGRAM_NAME): $(TARGET_DIRECTORY)/$(PROGRAM_NAME) | dist_dirs

$(TARGET_DIRECTORY)/$(PROGRAM_NAME): $(NON_MAIN_OBJECT_FILES) $(TARGET_DIRECTORY)/objects/$(MAIN_BASE_NAME).o | dist_dirs
	$(CXX) $(CXX_FLAGS) $(NON_MAIN_OBJECT_FILES) $(TARGET_DIRECTORY)/objects/$(MAIN_BASE_NAME).o -o $(TARGET_DIRECTORY)/$(PROGRAM_NAME)

$(ALTERNATIVE_MAIN_PREFIX)%: $(TARGET_DIRECTORY)/objects/$(ALTERNATIVE_MAIN_PREFIX)%.o \
	$(filter-out $(ALTERNATIVE_MAIN_PREFIX)%,$(NON_MAIN_OBJECT_FILES)) \
	$(filter-out $(MAIN_BASE_NAME).o,$(NON_MAIN_OBJECT_FILES))
	| dist_dirs
	$(CXX) $(CXX_FLAGS) $(NON_MAIN_OBJECT_FILES) $< -o $(TARGET_DIRECTORY)/$@

$(TEST_FILE_PREFIX)%: $(TARGET_DIRECTORY)/$(TEST_FILE_DIRECTORY)/$(TEST_FILE_PREFIX)%.o $(NON_MAIN_OBJECT_FILES) | dist_dirs
	$(CXX) $(CXX_FLAGS) $(NON_MAIN_OBJECT_FILES) -g $< -o $(TARGET_DIRECTORY)/$@

	@echo
	$(TARGET_DIRECTORY)/$@

$(TARGET_DIRECTORY)/$(TEST_FILE_DIRECTORY)/$(TEST_FILE_PREFIX)%.o: ./$(TEST_FILE_DIRECTORY)/$(TEST_FILE_PREFIX)%.cpp | dist_dirs
	$(CXX) $(CXX_FLAGS) -g -c $< -o $@

$(TARGET_DIRECTORY)/objects/%.o: ./$(SOURCE_DIRECTORY)/%.cpp $(HEADER_FILES) | dist_dirs
	$(CXX) $(CXX_FLAGS) -c $< -o $@

clean:
	rm -r $(TARGET_DIRECTORY)

dist_dirs:
	@mkdir -p $(TARGET_DIRECTORY)/objects
	@mkdir -p $(TARGET_DIRECTORY)/$(TEST_FILE_DIRECTORY)
	@mkdir -p $(DIST_DIRECTORY_STRUCTURE)

print: # Tests for wildcards
	@echo "PROGRAM_NAME: $(PROGRAM_NAME)"
	@echo "SOURCE_DIRECTORY: $(SOURCE_DIRECTORY)"
	@echo "MAIN_FILE: $(MAIN_FILE)"
	@echo "TARGET_DIRECTORY: $(TARGET_DIRECTORY)"
	@echo "TEST_FILE_DIRECTORY: $(TEST_FILE_DIRECTORY)"
	@echo "TEST_FILE_PREFIX: $(TEST_FILE_PREFIX)"

	@echo "CPP_FILES: $(CPP_FILES)"
	@echo "HEADER_FILES: $(HEADER_FILES)"
	@echo "OBJECT_FILES: $(OBJECT_FILES)"
	@echo "SOURCE_FILES: $(SOURCE_FILES)"
	@echo "SOURCE_DIRECTORY_STRUCTURE: $(SOURCE_DIRECTORY_STRUCTURE)"
	@echo "DIST_DIRECTORY_STRUCTURE: $(DIST_DIRECTORY_STRUCTURE)"
	@echo "NON_MAIN_OBJECT_FILES: $(NON_MAIN_OBJECT_FILES)"
	@echo "MAIN_OBJECT_FILES: $(MAIN_OBJECT_FILES)"
