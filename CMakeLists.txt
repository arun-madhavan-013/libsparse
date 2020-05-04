cmake_minimum_required(VERSION 3.0.0)

project(libsparse VERSION 30.0.0 LANGUAGES CXX)

set(DESCRIPTION "Linux port of android's sparse library(platform-tools-30.0.0).")
set(DEFAULT_BUILD_TYPE "Release")

if(NOT DEFINED BUILD_SHARED_LIBS)
	option(BUILD_SHARED_LIBS "Build a shared library form of libsparse." ON)
endif()


if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
	message(STATUS "Setting build type to '${DEFAULT_BUILD_TYPE}' as none was specified.")
	set(CMAKE_BUILD_TYPE "${DEFAULT_BUILD_TYPE}" CACHE STRING "Choose the type of build." FORCE)
	set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

# Dependencies
find_package(PkgConfig REQUIRED)
find_package(ZLIB REQUIRED zlib)
set(PKGCONF_REQ_PUB "zlib")

include(GNUInstallDirs)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

# Section: libsparse
set(SPARSE_SRC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/backed_block.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/output_file.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/sparse.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/sparse_crc32.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/sparse_err.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/sparse_read.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/android-base/stringprintf.cpp)
add_library(sparse SHARED ${SPARSE_SRC_FILES})
target_link_libraries(sparse ${ZLIB_LIBRARIES})
set_target_properties(sparse PROPERTIES
	VERSION ${PROJECT_VERSION}
	SOVERSION ${PROJECT_VERSION} 
	PUBLIC_HEADER ${CMAKE_CURRENT_SOURCE_DIR}/include/sparse/sparse.h)

# Section: simg2img
set(SIMG2IMG_SRC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/simg2img.cpp)
add_executable(simg2img ${SIMG2IMG_SRC_FILES})
target_link_libraries(simg2img sparse)

# Section: simg2simg
set(SIMG2SIMG_SRC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/simg2simg.cpp)
add_executable(simg2simg ${SIMG2SIMG_SRC_FILES})
target_link_libraries(simg2simg sparse)

# Section: img2simg
set(IMG2SIMG_SRC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/img2simg.cpp)
add_executable(img2simg ${IMG2SIMG_SRC_FILES})
target_link_libraries(img2simg sparse)

# Section: append2simg
set(APPEND2SIMG_SRC_FILES ${CMAKE_CURRENT_SOURCE_DIR}/append2simg.cpp)
add_executable(append2simg ${APPEND2SIMG_SRC_FILES})
target_link_libraries(append2simg sparse)

# Install products
install(TARGETS sparse simg2img simg2simg img2simg append2simg
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
	RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
	PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# Install pkg-config files
configure_file(libsparse.pc.in libsparse.pc @ONLY)
install(FILES ${CMAKE_BINARY_DIR}/libsparse.pc
	DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/pkgconfig)
