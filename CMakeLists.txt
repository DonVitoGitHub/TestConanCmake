cmake_minimum_required(VERSION 3.22)
project(debug)


#cmake settings
set (CMAKE_CXX_STANDARD 20)


#conan settings
list(APPEND CMAKE_MODULE_PATH ${CMAKE_BINARY_DIR})
list(APPEND CMAKE_PREFIX_PATH ${CMAKE_BINARY_DIR})

add_definitions("-std=c++20")
if(NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
  message(STATUS "Downloading conan.cmake from https://github.com/conan-io/cmake-conan")
  file(DOWNLOAD "https://raw.githubusercontent.com/conan-io/cmake-conan/0.18.1/conan.cmake"
                "${CMAKE_BINARY_DIR}/conan.cmake"
                TLS_VERIFY ON)
endif()
include(${CMAKE_BINARY_DIR}/conan.cmake)

#conan dependencies
conan_cmake_configure(REQUIRES boost/1.81.0
                      GENERATORS cmake_find_package)
conan_cmake_autodetect(settings)
conan_cmake_install(PATH_OR_REFERENCE .
                    BUILD missing
                    REMOTE conancenter
                    SETTINGS ${settings})
                  
#cmake build
set(Boost_USE_STATIC_LIBS ON)
find_package(Boost 1.81 COMPONENTS log REQUIRED)                   
add_executable(main main.cpp)
target_link_libraries(main Boost::log)
target_include_directories(main PUBLIC ${Boost_INCLUDE_DIR}) 
