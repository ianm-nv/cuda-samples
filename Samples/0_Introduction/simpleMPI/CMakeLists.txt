cmake_minimum_required(VERSION 3.20)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../../cmake/Modules")

project(simpleMPI LANGUAGES C CXX CUDA)

find_package(CUDAToolkit REQUIRED)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

set(CMAKE_CUDA_ARCHITECTURES 75 80 86 87 89 90 100 120)
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -Wno-deprecated-gpu-targets")
if(ENABLE_CUDA_DEBUG)
    set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -G")        # enable cuda-gdb (may significantly affect performance on some targets)
else()
    set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -lineinfo") # add line information to all builds for debug tools (exclusive to -G option)
endif()

# Include directories and libraries
include_directories(../../../Common)

find_package(MPI)

# Source file
if(${MPI_FOUND})
    # Add target for simpleMPI
    add_executable(simpleMPI simpleMPI.cpp simpleMPI.cu)

target_compile_options(simpleMPI PRIVATE $<$<COMPILE_LANGUAGE:CUDA>:--extended-lambda>)

target_compile_features(simpleMPI PRIVATE cxx_std_17 cuda_std_17)

    set_target_properties(simpleMPI PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

    target_include_directories(simpleMPI PUBLIC
        ${MPI_INCLUDE_PATH}
    )

    target_link_libraries(simpleMPI PUBLIC
        ${MPI_CXX_LIBRARIES}
    )

else()
    message(STATUS "MPI not found - will not build sample 'simpleMPI'")
endif()
