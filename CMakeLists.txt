cmake_minimum_required(VERSION 3.1)

project(god-taga LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    # set(CMAKE_BUILD_TYPE "Release")
    set(CMAKE_BUILD_TYPE "Debug")
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif ()
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")

# ----- Set options for debugging -----

set(USE_CCACHE ON CACHE BOOL "Use ccache to accelerate build")
find_program(CCACHE_EXE ccache)
if (USE_CCACHE AND CCACHE_EXE)
    set_property(GLOBAL PROPERTY RULE_LAUNCH_COMPILE "${CCACHE_EXE}")
    set_property(GLOBAL PROPERTY RULE_LAUNCH_LINK "${CCACHE_EXE}")
    message(STATUS "ccache: Enabled")
else ()
    set(USE_CCACHE OFF)
    message(STATUS "ccache: Disabled")
endif ()

set(USE_SANITIZER OFF CACHE BOOL "Enable Address/Memory sanitizer (set env as ASAN_OPTIONS=detect_leaks=1)")
if (USE_SANITIZER)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fno-omit-frame-pointer -fsanitize=address")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-omit-frame-pointer -fsanitize=address")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fno-omit-frame-pointer -fsanitize=address")
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -fno-omit-frame-pointer -fsanitize=address")
    message(STATUS "Address/Memory sanitizer: ENABLED")
else ()
    message(STATUS "Address/Memory sanitizer: DISABLED")
endif ()

# ----- Set compiler options -----

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")

set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -Og")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Og -DEIGEN_INITIALIZE_MATRICES_BY_NAN")

set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -O3 -ffast-math")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -ffast-math -DEIGEN_INITIALIZE_MATRICES_BY_NAN")

set(BUILD_WITH_MARCH_NATIVE OFF CACHE BOOL "Enable architecture-aware optimization (-march=native)")
if (BUILD_WITH_MARCH_NATIVE)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -mtune=native -march=native")
    set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -mtune=native -march=native -DEIGEN_INITIALIZE_MATRICES_BY_NAN")
    message(STATUS "Architecture-aware optimization (-march=native): ENABLED")
else ()
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -mtune=native")
    set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -mtune=native -DEIGEN_INITIALIZE_MATRICES_BY_NAN")
    message(STATUS "Architecture-aware optimization (-march=native): DISABLED")
endif ()



# ----- Find dependenies -----
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/3rd/eigen/)

find_package(Threads REQUIRED)

# spdlog
find_package(spdlog REQUIRED)

# fmt
find_package(fmt)

# taskflow
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/3rd/taskflow/)

add_subdirectory(3rd/box2d)


add_subdirectory(example)
