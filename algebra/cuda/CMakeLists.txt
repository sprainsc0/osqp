file(
  GLOB
  SRC_FILES
  CONFIGURE_DEPENDS
  *.h
  *.cu
  src/*.cu
  lin_sys/indirect/*.h
  lin_sys/indirect/*.cu)

target_sources(OSQPLIB PRIVATE ${SRC_FILES})

target_include_directories(OSQPLIB PRIVATE ${CMAKE_CURRENT_SOURCE_DIR} include lin_sys/indirect)

option(OSQP_CUDA_STATIC_LINKING "Statically link all CUDA libraries (WARNING: This will greatly increase the file size of the library)" OFF)
mark_as_advanced(OSQP_CUDA_STATIC_LINKING)

if(OSQP_CUDA_STATIC_LINKING)
  message(STATUS "Statically linking CUDA libraries")

  # cublasLt_static is a dependency of cublas_static.
  target_link_libraries(OSQPLIB CUDA::cudart CUDA::cublas_static CUDA::cusparse_static CUDA::cublasLt_static)
else()
  target_link_libraries(OSQPLIB CUDA::cudart CUDA::cublas CUDA::cusparse)
endif()

configure_file( ${CMAKE_CURRENT_SOURCE_DIR}/findAlgebraDependency.cmake.in
                ${CMAKE_CURRENT_BINARY_DIR}/../../osqp-findAlgebraDependency.cmake
                @ONLY )

