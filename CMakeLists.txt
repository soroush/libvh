cmake_minimum_required(VERSION 3.16.0)

project(libvh LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
SET(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

# Check for dependencies
find_package(CUDA REQUIRED)
find_path(THRUST_INCLUDE_DIRS "thrust/addressof.h")

enable_language("CUDA")
include_directories(${CUDA_INCLUDE_DIRS})
link_directories(${CUDA_LIBRARY_DIRS})
add_definitions(-DUSE_CUDA)
set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS} --expt-extended-lambda)

add_library(${PROJECT_NAME} INTERFACE)

target_include_directories(${PROJECT_NAME}
    INTERFACE $<INSTALL_INTERFACE:include> $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

# Installation
# Installation
include(GNUInstallDirs)

file(GLOB_RECURSE ALL_HEADER_FILES "${CMAKE_SOURCE_DIR}/include/*.cuh")

set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER "${ALL_HEADER_FILES}")

export(TARGETS ${PROJECT_NAME} FILE "${PROJECT_NAME}Config.cmake")

install(TARGETS ${PROJECT_NAME}
    EXPORT "${PROJECT_NAME}Config"
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/libvh
)

install(EXPORT "${PROJECT_NAME}Config"
    FILE "${PROJECT_NAME}Config.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/
)
