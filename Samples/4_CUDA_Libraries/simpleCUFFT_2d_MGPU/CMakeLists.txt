cmake_minimum_required(VERSION 3.20)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../../cmake/Modules")

project(simpleCUFFT_2d_MGPU LANGUAGES CUDA)

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

# Source file
# Add target for simpleCUFFT_2d_MGPU
add_executable(simpleCUFFT_2d_MGPU simpleCUFFT_2d_MGPU.cu)

if(MSVC)
    add_compile_definitions(_USE_MATH_DEFINES)
endif()

target_compile_options(simpleCUFFT_2d_MGPU PRIVATE $<$<COMPILE_LANGUAGE:CUDA>:--extended-lambda>)

target_compile_features(simpleCUFFT_2d_MGPU PRIVATE cxx_std_17 cuda_std_17)

set_target_properties(simpleCUFFT_2d_MGPU PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

target_link_libraries(simpleCUFFT_2d_MGPU PRIVATE
    CUDA::cufft
)
