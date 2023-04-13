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
TARGET_DIRECTORY := ./dist

CPP_FILES := $(filter-out $(SOURCE_DIRECTORY)/main.cpp, $(shell find $(SOURCE_DIRECTORY) -name "*.cpp"))
MAIN_FILE := $(shell find $(SOURCE_DIRECTORY) -name "main.cpp")
HEADER_FILES := $(shell find $(SOURCE_DIRECTORY) -name "*.hpp") $(shell find $(SOURCE_DIRECTORY) -name "*.h")
SOURCE_FILES := $(HEADER_FILES) $(CPP_FILES)
OBJECT_FILES := $(patsubst $(SOURCE_DIRECTORY)/%.cpp, ./dist/objects/%.o, $(CPP_FILES))

# For running tests
TESTNAME ?=

clean:
	rm -r ./dist/*

$(PROGRAM_NAME): $(OBJECT_FILES) ./dist/objects/main.o | dist_dir
	$(CXX) $(CXX_FLAGS) $(MAIN_FILE) -c -o ./dist/objects/main.o
	$(CXX) $(CXX_FLAGS) $(OBJECT_FILES) ./dist/objects/main.o -o ./dist/$(PROGRAM_NAME)

runtest: test_$(TESTNAME) | dist_dir
	./dist/$<

test_%: ./dist/tests/test_%.o $(OBJECT_FILES) | dist_dir # Makes test binaries
	$(CXX) $(CXX_FLAGS) $(OBJECT_FILES) -g $< -o ./dist/$@

	@echo
	./dist/$@


./dist/tests/test_%.o: ./tests/test_%.cpp | dist_dir
	$(CXX) $(CXX_FLAGS) -g -c $< -o $@

./dist/objects/%.o: ./src/%.cpp $(HEADER_FILES) | dist_dir # Makes object files
	$(CXX) $(CXX_FLAGS) -c $< -o $@

dist_dir:
	@mkdir -p ./dist/objects
	@mkdir -p ./dist/tests

print: # Tests for wildcards
	@echo "PROGRAM_NAME: $(PROGRAM_NAME)"
	@echo "CPP_FILES: $(CPP_FILES)"
	@echo "MAIN_FILE: $(MAIN_FILE)"
	@echo "OBJECT_FILES: $(OBJECT_FILES)"
	@echo "SOURCE_FILES: $(SOURCE_FILES)"
	@echo "TESTNAME: $(TESTNAME)"
