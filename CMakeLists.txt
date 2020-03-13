cmake_minimum_required(VERSION 3.10)
project(msp)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
elseif(MSVC)
  add_compile_options(/W4 /WX)
endif()

if(UNIX)
    set(BUILD_SHARED_LIBS "ON")
endif()

add_definitions(-DASIO_STANDALONE)
add_definitions(-DASIO_HAS_STD_ADDRESSOF)
add_definitions(-DASIO_HAS_STD_ARRAY)
add_definitions(-DASIO_HAS_CSTDINT)
add_definitions(-DASIO_HAS_STD_SHARED_PTR)
add_definitions(-DASIO_HAS_STD_TYPE_TRAITS)

if(WIN32)
    include_directories(${ASIO_HEADER_PATH})
endif()

OPTION(BUILD_EXAMPLES "Build Library with examples" ON)
OPTION(BUILD_TESTS "Build Library with tests" OFF)

find_package(Threads)
find_package(ament_cmake REQUIRED)

################################################################################
### libraries

# client library
add_library(msp src/Client.cpp src/PeriodicTimer.cpp)
target_include_directories(msp PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
                                      "$<INSTALL_INTERFACE:include>")
target_link_libraries(msp ${CMAKE_THREAD_LIBS_INIT})

################################################################################
### installation

ament_export_include_directories(include)
ament_export_libraries(msp)
ament_export_interfaces(export_msp HAS_LIBRARY_TARGET)

install(
  TARGETS msp
  EXPORT export_msp
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  INCLUDES
  DESTINATION include)

install(DIRECTORY include/ DESTINATION include)

ament_package()
