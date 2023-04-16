# Constants: Makefile

## Features
This Makefile allows for multiple source files to contain a main function while avoiding linker errors when linking two object files which both contain main functions. It can be easily used for any project, all that needs to be specified is the source directory and the target directory. By default, these values are `./src` and `./dist`

## Usage
This Makefile can be used by placing it in the root directory of a project. Above the line of `#` symbols, assign your desired include paths, linker flags, c++ compiler, c++ flags, source directory, and target directory by assigning `LIBRARY_INCLUDES`, `LXX_FLAGS`, `CXX_FLAGS`, `SOURCE_DIRECTORY`, `TARGET_DIRECTORY` respectively.

To make a file in `$(TARGET_DIRECTORY)/filename`, run `make filename`. This applies to files that are in subdirectories within the source directory. 
> (Ex: Running `make dir/foo` will make the file at `$(TARGET_DIRECTORY)/dir/foo`). 

Every time a file is meant to be built, all possible object files will be made. Then, all object files which do not contain a main function will be linked with the file you wish to build.
> (Ex: Running `make dir/foo` will link all non-main object files with `$(TARGET_DIRECTORY)/dir/foo`)

The final binary can be found in `$(TARGET_DIRECTORY)/bin/$(MAKECMDGOALS)` or in the symbolic link `./bin`
> (Ex: If running `make dir/foo` successfully compiles, the binary can be found in `$(TARGET_DIRECTORY)/bin/dir/foo` or in `./bin/dir/foo`)

## Requirements
- The existence of a `/dev/null`. If this does not exist, the variable `NULLFILE` can be configured to a local file such as `./dump`
- The `binutils` package (for the `nm` binary)
