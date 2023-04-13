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

##############################################################################

MAIN_BASE_NAME = $(notdir $(basename $(MAIN_FILE)))
CPP_FILES := $(filter-out $(MAIN_FILE), \
			 $(shell find $(SOURCE_DIRECTORY) -name "*.cpp") \
			 $(shell find $(SOURCE_DIRECTORY) -name "*.cxx") \
			 $(shell find $(SOURCE_DIRECTORY) -name "*.cc"))
HEADER_FILES := $(shell find $(SOURCE_DIRECTORY) -name "*.hpp") \
				$(shell find $(SOURCE_DIRECTORY) -name "*.h")
SOURCE_FILES := $(HEADER_FILES) $(CPP_FILES)
OBJECT_FILES := $(patsubst $(SOURCE_DIRECTORY)/%.cpp, $(TARGET_DIRECTORY)/objects/%.o, $(CPP_FILES))

.PHONY: clean print $(PROGRAM_NAME) dist_dir

$(PROGRAM_NAME): $(TARGET_DIRECTORY)/$(PROGRAM_NAME) | dist_dir

$(TARGET_DIRECTORY)/$(PROGRAM_NAME): $(OBJECT_FILES) $(TARGET_DIRECTORY)/objects/$(MAIN_BASE_NAME).o | dist_dir
	$(CXX) $(CXX_FLAGS) $(OBJECT_FILES) $(TARGET_DIRECTORY)/objects/$(MAIN_BASE_NAME).o -o $(TARGET_DIRECTORY)/$(PROGRAM_NAME)

$(TEST_FILE_PREFIX)%: $(TARGET_DIRECTORY)/$(TEST_FILE_DIRECTORY)/$(TEST_FILE_PREFIX)%.o $(OBJECT_FILES) | dist_dir
	$(CXX) $(CXX_FLAGS) $(OBJECT_FILES) -g $< -o $(TARGET_DIRECTORY)/$@

	@echo
	$(TARGET_DIRECTORY)/$@

$(TARGET_DIRECTORY)/$(TEST_FILE_DIRECTORY)/$(TEST_FILE_PREFIX)%.o: ./$(TEST_FILE_DIRECTORY)/$(TEST_FILE_PREFIX)%.cpp | dist_dir
	$(CXX) $(CXX_FLAGS) -g -c $< -o $@

$(TARGET_DIRECTORY)/objects/%.o: ./$(SOURCE_DIRECTORY)/%.cpp $(HEADER_FILES) | dist_dir
	$(CXX) $(CXX_FLAGS) -c $< -o $@

clean:
	rm -r $(TARGET_DIRECTORY)

dist_dir:
	@mkdir -p $(TARGET_DIRECTORY)/objects
	@mkdir -p $(TARGET_DIRECTORY)/$(TEST_FILE_DIRECTORY)

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
