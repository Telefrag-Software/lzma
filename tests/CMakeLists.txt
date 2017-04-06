include_directories("${CMAKE_CURRENT_LIST_DIR}/../src/common")

foreach(
    x
    test_bcj_exact_size
    test_block_header
    test_check
    test_filter_flags
    test_index
    test_stream_flags
)
  add_executable(${x} ${x}.c)
  target_link_libraries(${x} PUBLIC lzma)
endforeach()
