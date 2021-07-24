set(LZMA_SRCS
  common/sysdefs.h
  common/tuklib_integer.h
  liblzma/check/check.c
  liblzma/check/crc32_fast.c
  liblzma/check/crc32_table.c
  liblzma/check/crc64_fast.c
  liblzma/check/crc64_table.c
  liblzma/check/sha256.c
  liblzma/common/alone_decoder.c
  liblzma/common/alone_encoder.c
  liblzma/common/auto_decoder.c
  liblzma/common/block_buffer_decoder.c
  liblzma/common/block_buffer_encoder.c
  liblzma/common/block_decoder.c
  liblzma/common/block_encoder.c
  liblzma/common/block_header_decoder.c
  liblzma/common/block_header_encoder.c
  liblzma/common/block_util.c
  liblzma/common/common.c
  liblzma/common/easy_buffer_encoder.c
  liblzma/common/easy_decoder_memusage.c
  liblzma/common/easy_encoder.c
  liblzma/common/easy_encoder_memusage.c
  liblzma/common/easy_preset.c
  liblzma/common/filter_buffer_decoder.c
  liblzma/common/filter_buffer_encoder.c
  liblzma/common/filter_common.c
  liblzma/common/filter_decoder.c
  liblzma/common/filter_encoder.c
  liblzma/common/filter_flags_decoder.c
  liblzma/common/filter_flags_encoder.c
  liblzma/common/index.c
  liblzma/common/index_decoder.c
  liblzma/common/index_encoder.c
  liblzma/common/index_hash.c
  liblzma/common/stream_buffer_decoder.c
  liblzma/common/stream_buffer_encoder.c
  liblzma/common/stream_decoder.c
  liblzma/common/stream_encoder.c
  liblzma/common/stream_flags_common.c
  liblzma/common/stream_flags_decoder.c
  liblzma/common/stream_flags_encoder.c
  liblzma/common/vli_decoder.c
  liblzma/common/vli_encoder.c
  liblzma/common/vli_size.c
  liblzma/delta/delta_common.c
  liblzma/delta/delta_decoder.c
  liblzma/delta/delta_encoder.c
  liblzma/lz/lz_decoder.c
  liblzma/lz/lz_encoder.c
  liblzma/lz/lz_encoder_mf.c
  liblzma/lzma/fastpos_table.c
  liblzma/lzma/lzma2_decoder.c
  liblzma/lzma/lzma2_encoder.c
  liblzma/lzma/lzma_decoder.c
  liblzma/lzma/lzma_encoder.c
  liblzma/lzma/lzma_encoder_optimum_fast.c
  liblzma/lzma/lzma_encoder_optimum_normal.c
  liblzma/lzma/lzma_encoder_presets.c
  liblzma/rangecoder/price_table.c
  liblzma/simple/arm.c
  liblzma/simple/armthumb.c
  liblzma/simple/ia64.c
  liblzma/simple/powerpc.c
  liblzma/simple/simple_coder.c
  liblzma/simple/simple_decoder.c
  liblzma/simple/simple_encoder.c
  liblzma/simple/sparc.c
  liblzma/simple/x86.c
  )

add_library(lzma ${LZMA_SRCS})
target_compile_definitions(lzma PUBLIC "$<BUILD_INTERFACE:HAVE_CONFIG_H=1>")

target_include_directories(
    lzma
    PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/common>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/liblzma/api>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/liblzma/check>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/liblzma/common>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/liblzma/delta>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/liblzma/lz>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/liblzma/lzma>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/liblzma/rangecoder>"
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/liblzma/simple>"
)

if(MSVC AND NOT BUILD_SHARED_LIBS)
  # See 'windows/INSTALL-MSVC.txt', section 'Notes'
  target_compile_definitions(lzma INTERFACE "LZMA_API_STATIC")
endif()

if(CMAKE_C_COMPILER_ID STREQUAL "XL")
  # Disable the XL compiler optimizer because it causes crashes
  # and other bad behavior in liblzma code.
  set_property(TARGET lzma PROPERTY COMPILE_FLAGS "-qnooptimize")
elseif(CMAKE_C_COMPILER_ID STREQUAL "GNU" AND
       CMAKE_C_COMPILER_VERSION VERSION_LESS 3.4)
  # Disable the old GNU compiler optimizer.
  set_property(TARGET lzma PROPERTY COMPILE_FLAGS "-O0")
endif()

install(
    TARGETS lzma
    EXPORT "${targets_export_name}"
    LIBRARY DESTINATION "lib"
    ARCHIVE DESTINATION "lib"
    RUNTIME DESTINATION "bin"
    INCLUDES DESTINATION "${include_install_dir}"
)

install(
    FILES
    "liblzma/api/lzma.h"
    DESTINATION
    "${include_install_dir}"
)

install(
    DIRECTORY
    "liblzma/api/lzma"
    DESTINATION
    "${include_install_dir}"
)
