# Original version taken from:
# * https://github.com/Kitware/CMake/tree/b557f3e106274cfb4a08a48d2dd3b6d4f3f67bb7/Utilities/lzma

cmake_minimum_required(VERSION 3.0)
project(lzma VERSION 5.2.3)

option(LZMA_BUILD_TESTS "Build tests" OFF)

include(CheckCSourceCompiles)
include(CheckIncludeFile)
include(CheckSymbolExists)
include(CheckTypeSize)
include(TestBigEndian)

check_c_source_compiles(
  "int test (void *restrict x);\nint main (void) {return 0;}"
  HAVE_RESTRICT)

check_c_source_compiles(
"typedef struct abc *d;\nint test (d __restrict x);\nint main (void) {return 0;}"
  HAVE___RESTRICT)

check_c_source_compiles(
  "inline int test (void) {return 0;}\nint main (void) {return test();}"
  HAVE_INLINE)

check_c_source_compiles(
  "__inline int test (void) {return 0;}\nint main (void) {return test();}"
  HAVE___INLINE)

check_include_file(byteswap.h HAVE_BYTESWAP_H)
check_include_file(inttypes.h HAVE_INTTYPES_H)
check_include_file(limits.h HAVE_LIMITS_H)
check_include_file(memory.h HAVE_MEMORY_H)
check_include_file(strings.h HAVE_STRINGS_H)
check_include_file(string.h HAVE_STRING_H)
check_include_file(sys/sysctl.h HAVE_SYS_SYSCTL_H)

check_include_file(stdbool.h HAVE_STDBOOL_H)
if(NOT HAVE_STDBOOL_H)
  check_type_size(_Bool _BOOL)
endif()

check_c_source_compiles(
  "#include<byteswap.h>\nint main(void){bswap_16(0);return 0;}"
  HAVE_BSWAP_16)
check_c_source_compiles(
  "#include<byteswap.h>\nint main(void){bswap_32(0);return 0;}"
  HAVE_BSWAP_32)
check_c_source_compiles(
  "#include<byteswap.h>\nint main(void){bswap_64(0);return 0;}"
  HAVE_BSWAP_64)

test_big_endian(WORDS_BIGENDIAN)

set(HAVE_CHECK_CRC64 1)
set(HAVE_CHECK_SHA256 1)

set(HAVE_DECODER_ARM 1)
set(HAVE_DECODER_ARMTHUMB 1)
set(HAVE_DECODER_DELTA 1)
set(HAVE_DECODER_IA64 1)
set(HAVE_DECODER_LZMA1 1)
set(HAVE_DECODER_LZMA2 1)
set(HAVE_DECODER_POWERPC 1)
set(HAVE_DECODER_SPARC 1)
set(HAVE_DECODER_X86 1)

set(HAVE_ENCODER_ARM 1)
set(HAVE_ENCODER_ARMTHUMB 1)
set(HAVE_ENCODER_DELTA 1)
set(HAVE_ENCODER_IA64 1)
set(HAVE_ENCODER_LZMA1 1)
set(HAVE_ENCODER_LZMA2 1)
set(HAVE_ENCODER_POWERPC 1)
set(HAVE_ENCODER_SPARC 1)
set(HAVE_ENCODER_X86 1)

set(HAVE_MF_BT2 1)
set(HAVE_MF_BT3 1)
set(HAVE_MF_BT4 1)
set(HAVE_MF_HC3 1)
set(HAVE_MF_HC4 1)

check_type_size(int16_t INT16_T)
check_type_size(int32_t INT32_T)
check_type_size(int64_t INT64_T)
check_type_size(intmax_t INTMAX_T)
check_type_size(uint8_t UINT8_T)
check_type_size(uint16_t UINT16_T)
check_type_size(uint32_t UINT32_T)
check_type_size(uint64_t UINT64_T)
check_type_size(uintmax_t UINTMAX_T)

check_type_size("short" SIZE_OF_SHORT)
check_type_size("int" SIZE_OF_INT)
check_type_size("long" SIZE_OF_LONG)
check_type_size("long long" SIZE_OF_LONG_LONG)

check_type_size("unsigned short" SIZE_OF_UNSIGNED_SHORT)
check_type_size("unsigned" SIZE_OF_UNSIGNED)
check_type_size("unsigned long" SIZE_OF_UNSIGNED_LONG)
check_type_size("unsigned long long" SIZE_OF_UNSIGNED_LONG_LONG)
check_type_size("size_t" SIZE_OF_SIZE_T)

check_type_size("__int64" __INT64)
check_type_size("unsigned __int64" UNSIGNED___INT64)

check_type_size(uintptr_t UINTPTR_T)
if(NOT HAVE_UINTPTR_T)
  if("${CMAKE_SIZEOF_VOID_P}" EQUAL 8)
    set(uintptr_t "uint64_t")
  else()
    set(uintptr_t "uint32_t")
  endif()
endif()

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")
configure_file("cmake/config.h.in" "${generated_dir}/config.h" @ONLY)
include_directories(${generated_dir})

####
# Installation (https://github.com/forexample/package-example)

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

# Note: PROJECT_VERSION is used as a VERSION
write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

# Use variables:
#   * targets_export_name
#   * PROJECT_NAME
configure_package_config_file(
    "cmake/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)

add_subdirectory(src)

if(LZMA_BUILD_TESTS)
  add_subdirectory(tests)
endif()
