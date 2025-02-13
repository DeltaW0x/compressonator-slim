cmake_minimum_required(VERSION 3.10)
project(CMP_Core)
add_library(CMP_Core STATIC)

target_sources(CMP_Core
    PRIVATE
    cmp_core/shaders/bc1_encode_kernel.h
    cmp_core/shaders/bc1_common_kernel.h
    cmp_core/shaders/bc1_encode_kernel.cpp
    cmp_core/shaders/bc2_encode_kernel.h
    cmp_core/shaders/bc2_encode_kernel.cpp
    cmp_core/shaders/bc3_encode_kernel.h
    cmp_core/shaders/bc3_encode_kernel.cpp
    cmp_core/shaders/bc4_encode_kernel.h
    cmp_core/shaders/bc4_encode_kernel.cpp
    cmp_core/shaders/bc5_encode_kernel.h
    cmp_core/shaders/bc5_encode_kernel.cpp
    cmp_core/shaders/bc6_encode_kernel.h
    cmp_core/shaders/bc6_encode_kernel.cpp
    cmp_core/shaders/bc7_encode_kernel.h
    cmp_core/shaders/bc7_common_encoder.h
    cmp_core/shaders/bc7_encode_kernel.cpp
    cmp_core/shaders/bcn_common_kernel.h
    cmp_core/shaders/bcn_common_api.h
    cmp_core/shaders/common_def.h

    cmp_core/source/cmp_core.h
    cmp_core/source/cmp_core.cpp
    cmp_core/source/cmp_math_vec4.h
    cmp_core/source/cmp_math_func.h

    applications/_libs/cmp_math/cpu_extensions.cpp
    applications/_libs/cmp_math/cmp_math_common.cpp
)

target_include_directories(CMP_Core
    PUBLIC
    cmp_core/shaders
    cmp_core/source
    applications/_libs/cmp_math/
)

if (UNIX)
    target_compile_definitions(CMP_Core PRIVATE _LINUX ASPM_GPU)
endif()

# Core SIMD options

# SSE
add_library(CMP_Core_SSE STATIC)
target_sources(CMP_Core_SSE PRIVATE cmp_core/source/core_simd_sse.cpp)
target_include_directories(CMP_Core_SSE PRIVATE cmp_core/source cmp_core/shaders)

if (UNIX)
    target_compile_options(CMP_Core_SSE PRIVATE -march=nehalem)
endif()

# AVX
add_library(CMP_Core_AVX STATIC)
target_sources(CMP_Core_AVX PRIVATE cmp_core/source/core_simd_avx.cpp)
target_include_directories(CMP_Core_AVX PRIVATE cmp_core/source cmp_core/shaders)

if (WIN32)
    target_compile_options(CMP_Core_AVX PRIVATE /arch:AVX2)
else()
    target_compile_options(CMP_Core_AVX PRIVATE -march=haswell)
endif()

# AVX-512
add_library(CMP_Core_AVX512 STATIC)
target_sources(CMP_Core_AVX512 PRIVATE cmp_core/source/core_simd_avx512.cpp)
target_include_directories(CMP_Core_AVX512 PRIVATE cmp_core/source cmp_core/shaders)

if (WIN32)
    target_compile_options(CMP_Core_AVX512 PRIVATE /arch:AVX-512)
else()
    target_compile_options(CMP_Core_AVX512 PRIVATE -march=knl)
endif()

# Link SIMD libraries to CMP_Core
target_link_libraries(CMP_Core PRIVATE CMP_Core_SSE CMP_Core_AVX CMP_Core_AVX512)
