set(TORCH_RPC_TEST_DIR "${TORCH_ROOT}/test/cpp/rpc")
set(TORCH_RPC_TEST_SOURCES
  ${TORCH_ROOT}/test/cpp/common/main.cpp
  ${TORCH_RPC_TEST_DIR}/test_wire_serialization.cpp
  ${TORCH_RPC_TEST_DIR}/test_tensorpipe_serialization.cpp
  ${TORCH_RPC_TEST_DIR}/test_e2e_process_group.cpp
)

add_executable(test_cpp_rpc ${TORCH_RPC_TEST_SOURCES})
target_include_directories(
  test_cpp_rpc PRIVATE
  ${ATen_CPU_INCLUDE}
  ${TORCH_ROOT}/third_party/tensorpipe)
target_link_libraries(test_cpp_rpc PRIVATE torch c10d gtest process_group_agent)

if(USE_CUDA)
  target_link_libraries(test_cpp_rpc PRIVATE
    ${CUDA_LIBRARIES}
    ${CUDA_NVRTC_LIB}
    ${CUDA_CUDA_LIB}
    ${TORCH_CUDA_LIBRARIES})

  target_compile_definitions(test_cpp_rpc PRIVATE "USE_CUDA")
endif()

if(INSTALL_TEST)
  install(TARGETS test_cpp_rpc DESTINATION bin)
  # Install PDB files for MSVC builds
  if(MSVC AND BUILD_SHARED_LIBS)
    install(FILES $<TARGET_PDB_FILE:test_cpp_rpc> DESTINATION bin OPTIONAL)
  endif()
endif()
