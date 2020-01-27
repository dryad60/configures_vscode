cmake_minimum_required(VERSION 3.8)
project(app_simple)


add_executable(cw_FramingEngine main.cpp
                                ParamParser.cpp
                                CrashHelper.cpp
                                EngineDirector.cpp)


target_include_directories(cw_FramingEngine PRIVATE
                            ${CMAKE_CURRENT_SOURCE_DIR}/../ParamConfigModule
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/generalbusiness/include
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/generalbusiness/GeneralRPCModule
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/basic
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/basic/utils
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/basic/memorypool
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/cwlogger
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/shmmq
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/include
                            ${CMAKE_CURRENT_SOURCE_DIR}/../submodule/Cpp-Foundation/src/codecunit/include
                            ${Poco_ROOT_DIR}/include
                            ${GRPC_INCLUDE_DIR}
                            ${Protobuf_INCLUDE_DIR}
                            ${TurboJPEG_INCLUDE}
                            ${JsonCPP_INCLUDE_DIR}
                            ${Boost_INCLUDE_DIRS}
                            ${CONAN_INCLUDE_DIRS_HEADSHOULDER_SDK_CWKEY}
                            ${OPENCV_INCLUDE_DIR})

target_link_libraries(cw_FramingEngine PRIVATE
                      ${Poco_Json_LIBRARY}
                      ${Poco_Foundation_LIBRARY}
                      ${Poco_Util_LIBRARY}
                      ${Poco_JSON_LIBRARY}
                      basic
                      cwlogger
                      ParamConfigModule
                      GeneralRPCModule
                      codecunit
                      ${TBB_IMPORTED_TARGETS}
                      ${TurboJPEG_LIBRARY}
                      ${JPEG_LIBRARY}
                      pthread
                      ${JsonCPP_LIBRARY}
                      stdc++fs)

target_compile_features(cw_FramingEngine PRIVATE cxx_std_17)
set_property(TARGET cw_FramingEngine PROPERTY CXX_STANDARD 17)
set_property(TARGET cw_FramingEngine PROPERTY CXX_STANDARD_REQUIRED ON)

add_subdirectory(base)
add_subdirectory(gadget)

if (ENGINE_ExtractedFrame)
  add_subdirectory(engine/ExtractedFrame)
  target_link_libraries(cw_FramingEngine PRIVATE cubeExtractedFrame)
elseif(ENGINE_HeadShoulder)
  add_subdirectory(engine/HeadShoulder)
  target_link_libraries(cw_FramingEngine PRIVATE cubeHeadShoulder)
else()
  message(FATAL_ERROR  "FATAL-ERROR:引擎类型未指定,需要[ENGINE_ExtractedFrame/ENGINE_HeadShoulder/...]")
endif()

add_custom_command(TARGET cw_FramingEngine POST_BUILD COMMAND echo ARGS ${STR_ENGINETYPE})

