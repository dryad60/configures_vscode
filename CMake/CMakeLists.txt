cmake_minimum_required(VERSION 3.8)
project(FramingEngine)

#编译参数,影响工程的编译文件和链接依赖so和代码里启用哪一个Thrift消息Handler
set(ENGINE_FRAME NO)
set(ENGINE_HEADSHOULDER YES)
add_definitions(-DENGINE_HEADSHOULDER)

#cmake conan install下载Atifactory上的底层依赖
if(NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
   message(STATUS "Downloading conan.cmake from https://github.com/conan-io/cmake-conan")
   file(DOWNLOAD "https://github.com/conan-io/cmake-conan/raw/v0.14/conan.cmake"
                 "${CMAKE_BINARY_DIR}/conan.cmake")
endif()
include(${CMAKE_BINARY_DIR}/conan.cmake)
conan_cmake_install(CONANFILE ../conanfile.py
                BASIC_SETUP
                SETTINGS build_type=Release
                SETTINGS compiler.version=4.8)
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)

#通过FindXXX.cmake机制来引入依赖库
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake_find)

set(Poco_DIR ${CONAN_POCO_ROOT})
find_package(Poco REQUIRED COMPONENTS Net Util)
set(BOOST_ROOT ${CONAN_BOOST_ROOT})
find_package(Boost REQUIRED COMPONENTS thread system)
set(TBB_DIR "${CONAN_TBB_ROOT}/cmake")
find_package(TBB CONFIG REQUIRED COMPONENTS tbb tbbmalloc_proxy tbbmalloc)
set(Protobuf_LOCATION ${CONAN_PROTOBUF_ROOT})
find_package(Protobuf REQUIRED)
set(GRPC_ROOT_DIR ${CONAN_GRPC_ROOT})
find_package(gRPC REQUIRED)
set(TurboJPEG_LOCATION ${CONAN_LIBJPEG-TURBO_ROOT})
find_package(TurboJPEG REQUIRED)
set(JsonCPP_LOCATION ${CONAN_JSONCPP_ROOT})
find_package(JsonCPP REQUIRED)

add_subdirectory(app_simple)
add_subdirectory(ParamConfigModule)
add_subdirectory(submodule/Cpp-Foundation/src)




#安装指令
set(CMAKE_INSTALL_PREFIX ${CMAKE_BINARY_DIR}/Install_FramingEngine)
INSTALL(TARGETS cw_FramingEngine basic codecunit cwlogger GeneralRPCModule ParamConfigModule ResultPostProcessModule shmmq
       RUNTIME DESTINATION AlgorithmFrame
       LIBRARY DESTINATION AlgorithmFrame
       ARCHIVE DESTINATION AlgorithmFrame)
INSTALL(DIRECTORY ${CMAKE_BINARY_DIR}/app_simple/config DESTINATION AlgorithmFrame)
INSTALL(DIRECTORY ${CMAKE_BINARY_DIR}/app_simple/headshoulderdet_plugin DESTINATION AlgorithmFrame)
INSTALL(FILES
      ${CMAKE_CURRENT_SOURCE_DIR}/app_simple/engine/ExtractedFrame/ExtractedFrame.proto
       DESTINATION AlgorithmFrame)
