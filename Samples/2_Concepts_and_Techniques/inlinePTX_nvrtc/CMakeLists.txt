cmake_minimum_required(VERSION 3.20)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../../cmake/Modules")

project(inlinePTX_nvrtc LANGUAGES C CXX CUDA)

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
# Add target for inlinePTX_nvrtc
add_executable(inlinePTX_nvrtc inlinePTX.cpp)

target_compile_options(inlinePTX_nvrtc PRIVATE $<$<COMPILE_LANGUAGE:CUDA>:--extended-lambda>)

target_compile_features(inlinePTX_nvrtc PRIVATE cxx_std_17 cuda_std_17)

set_target_properties(inlinePTX_nvrtc PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

target_link_libraries(inlinePTX_nvrtc PRIVATE
    CUDA::nvrtc
    CUDA::cuda_driver
)

# Copy clock_kernel.cu to the output directory
add_custom_command(TARGET inlinePTX_nvrtc POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    ${CMAKE_CURRENT_SOURCE_DIR}/inlinePTX_kernel.cu ${CMAKE_CURRENT_BINARY_DIR}
)
