cmake_minimum_required(VERSION 3.20)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../../cmake/Modules")

project(simpleD3D11 LANGUAGES C CXX CUDA)

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

if(WIN32)
    # Source file
    # Add target for simpleD3D11
    add_executable(simpleD3D11
        simpleD3D11.cpp
        sinewave_cuda.cu
        ../../../Common/rendercheck_d3d11.cpp
    )

    target_compile_options(simpleD3D11 PRIVATE $<$<COMPILE_LANGUAGE:CUDA>:--extended-lambda>)

    target_compile_features(simpleD3D11 PRIVATE cxx_std_17 cuda_std_17)

    set_target_properties(simpleD3D11 PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

    target_include_directories(simpleD3D11 PRIVATE
        ${CUDAToolkit_INCLUDE_DIRS}
    )

    target_link_libraries(simpleD3D11 PRIVATE
        d3d11
        dxgi
        dxguid
        d3dcompiler
    )

    add_custom_command(TARGET simpleD3D11 POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${CMAKE_CURRENT_SOURCE_DIR}/data
        ${CMAKE_CURRENT_BINARY_DIR}/data
    )
else()
    message(STATUS "Sample 'simpleD3D11' is Windows-only - skipping")
endif()
