cmake_minimum_required(VERSION 3.0)

project(minorminer)
# set(VERSION_MAJOR 2)
# set(VERSION_MINOR 5)
# set(VERSION_PATCH 0)

option(MINORMINER_BUILD_CLIB "Build C library." OFF)
option(MINORMINER_BUILD_TESTS "Build unit tests." OFF)
option(MINORMINER_BUILD_EXAMPLES "Build examples." OFF)

add_library(minorminer INTERFACE)
target_include_directories(minorminer INTERFACE ${PROJECT_SOURCE_DIR}/include)

if(MINORMINER_BUILD_CLIB)
  add_subdirectory(clib)
endif()

if(MINORMINER_BUILD_TESTS)
    # Download and unpack googletest at configure time
    configure_file(CMakeLists.txt.in googletest-download/CMakeLists.txt)
    execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
        RESULT_VARIABLE result
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download )
    if(result)
        message(FATAL_ERROR "CMake step for googletest failed: ${result}")
    endif()
    execute_process(COMMAND ${CMAKE_COMMAND} --build .
        RESULT_VARIABLE result
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download )
    if(result)
        message(FATAL_ERROR "Build step for googletest failed: ${result}")
    endif()

    # Prevent overriding the parent project's compiler/linker
    # settings on Windows
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

    # Add googletest directly to our build. This defines
    # the gtest and gtest_main targets.
    add_subdirectory(${CMAKE_BINARY_DIR}/googletest-src
                     ${CMAKE_BINARY_DIR}/googletest-build
                     EXCLUDE_FROM_ALL)

    # The gtest/gtest_main targets carry header search path
    # dependencies automatically when using CMake 2.8.11 or
    # later. Otherwise we have to add them here ourselves.
    if (CMAKE_VERSION VERSION_LESS 2.8.11)
        include_directories("${gtest_SOURCE_DIR}/include")
    endif()

    enable_testing()
    add_subdirectory(tests)
endif()

if(MINORMINER_BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()
