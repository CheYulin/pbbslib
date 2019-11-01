cmake_minimum_required(VERSION 3.6)
project(pbbslib)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 11)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH}) # To allow CMake to locate our Find*.cmake files

option(ENABLE_EDGE_LONG "enable edge long" OFF)
option(DUMP_COMPILATION_TO_FILE "dump compilation to files" OFF)

if (ENABLE_EDGE_LONG)
    add_definitions(-DEDGELONG)
endif ()

## Include CMake Configs.
include(SetSIMDFlages)
include(SetLibraries)
if (OPENMP_FOUND)
    add_definitions(-DOPENMP)
else ()
    # HOMEGROWN
    add_definitions(-DHOMEGROWN)
    link_libraries(${CMAKE_THREAD_LIBS_INIT})
endif ()
if (Tcmalloc_FOUND)
    link_libraries(${Tcmalloc_LIBRARY})
endif ()

include_directories(.)
include_directories(examples)
include_directories(strings)

add_definitions(-DUSE_LOG -DUSEMALLOC -DNDEBUG)
add_definitions(-DAMORTIZEDPD -DUSEMALLOC -DNDEBUG)
add_compile_options(-mcx16 -ldl -march=native -Wall -O3 -g)

## Show Compilation Commands
if (NOT DUMP_COMPILATION_TO_FILE)
    set(CMAKE_VERBOSE_MAKEFILE ON)
else ()
    set(CMAKE_EXPORT_COMPILE_COMMANDS ON)   # dump complilation cmmands to compile_commands.json
endif ()

## Examples
add_executable(mcss examples/mcss.cpp)
add_executable(wc examples/wc.cpp)
add_executable(grep examples/grep.cpp)
add_executable(build_index examples/build_index.cpp)
add_executable(primes examples/primes.cpp)
add_executable(longest_repeated_substring examples/longest_repeated_substring.cpp)
add_executable(bw examples/bw.cpp)

## Other Tests
add_executable(test_alloc test_alloc.cpp)
add_executable(test_scheduler test_scheduler.cpp)
add_executable(test_sort test_sort.cpp)
add_executable(time_tests time_tests.cpp)

## Suffix Tree Tests
add_executable(test_suffix_tree strings/test_suffix_tree.cpp)

# Not Compiled Yet. Multiple Definitions.
#add_executable(test_suffix_array strings/test/suffix_array.cpp
#        strings/test/suffix_array_test.cpp)
#

