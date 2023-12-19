# Define the minimum required version of cmake
cmake_minimum_required(VERSION 3.10)

# Define the project name and version
project(avrdisas VERSION 0.07)

# Specify the C standard
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED True)

# Set the compiler options
add_compile_options(-O2 -Wall -D_POSIX_SOURCE -D_XOPEN_SOURCE=500 -DAVRDISAS_VERSION="${PROJECT_VERSION}")

# Include directories
include_directories(includes)

# Define the source and header files
set(SOURCES
    src/avrdisas.c
    src/JumpCall.c
    src/Callbacks_Assembly.c
    src/Callbacks_PseudoCode.c
    src/Options.c
    src/IORegisters.c
    src/MNemonics.c
    src/Tagfile.c
    src/Tools.c
)

# Define the executable
add_executable(avrdisas ${SOURCES})

# Install rules
install(TARGETS avrdisas DESTINATION /usr/bin)
install(FILES avrdisas.conf DESTINATION /etc)

# Custom target for creating a distribution tarball
set(DIST_DIR "${CMAKE_PROJECT_NAME}-${PROJECT_VERSION}")
add_custom_target(dist
    COMMAND ${CMAKE_COMMAND} -E make_directory "${DIST_DIR}"
    COMMAND ${CMAKE_COMMAND} -E copy_if_different $<TARGET_FILE:avrdisas> "${DIST_DIR}"
    COMMAND ${CMAKE_COMMAND} -E tar "cvfj" "${DIST_DIR}.tar.bz2" --format=bzip2 "${DIST_DIR}"
    COMMAND ${CMAKE_COMMAND} -E remove_directory "${DIST_DIR}"
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)

# Add custom targets for test, debug, valgrind if needed
add_custom_target(test
    COMMAND ${CMAKE_BINARY_DIR}/avrdisas [your-test-options-here]
)

add_custom_target(debug
    COMMAND gdb --args ${CMAKE_BINARY_DIR}/avrdisas [your-debug-options-here]
)

add_custom_target(valgrind
    COMMAND valgrind ${CMAKE_BINARY_DIR}/avrdisas [your-valgrind-options-here]
)

