cmake_minimum_required(VERSION 3.21)
project(ContentManager VERSION 0.9.0)
include(../../cmake/common.cmake)
cmake_policy(SET CMP0091 NEW)

message("CMAKE_PREFIX_PATH = ${CMAKE_PREFIX_PATH}")
message("CMAKE_CURRENT_BINARY_DIR = ${CMAKE_CURRENT_BINARY_DIR}")

set(CMAKE_CXX_STANDARD 20)

SET(CMAKE_EXPORT_COMPILE_COMMANDS 1)

find_package(gRPC CONFIG REQUIRED)

#user proto
get_filename_component(user_proto "user.proto" ABSOLUTE)
get_filename_component(user_proto_path "${user_proto}" PATH)

set(user_proto_srcs "${CMAKE_CURRENT_BINARY_DIR}/user.pb.cc")
set(user_proto_hdrs "${CMAKE_CURRENT_BINARY_DIR}/user.pb.h")
set(user_grpc_srcs "${CMAKE_CURRENT_BINARY_DIR}/user.grpc.pb.cc")
set(user_grpc_hdrs "${CMAKE_CURRENT_BINARY_DIR}/user.grpc.pb.h")
add_custom_command(
      OUTPUT "${user_proto_srcs}" "${user_proto_hdrs}" "${user_grpc_srcs}" "${user_grpc_hdrs}"
      COMMAND ${_PROTOBUF_PROTOC}
      ARGS --grpc_out "${CMAKE_CURRENT_BINARY_DIR}"
        --cpp_out "${CMAKE_CURRENT_BINARY_DIR}"
        -I "${user_proto_path}"
        --plugin=protoc-gen-grpc="${_GRPC_CPP_PLUGIN_EXECUTABLE}"
        "${user_proto}"
      DEPENDS "${user_proto}"
      COMMENT "Building user protos..."
)

#ppos proto
get_filename_component(ppos_proto "ppos.proto" ABSOLUTE)
get_filename_component(ppos_proto_path "${ppos_proto}" PATH)

set(ppos_proto_srcs "${CMAKE_CURRENT_BINARY_DIR}/ppos.pb.cc")
set(ppos_proto_hdrs "${CMAKE_CURRENT_BINARY_DIR}/ppos.pb.h")
set(ppos_grpc_srcs "${CMAKE_CURRENT_BINARY_DIR}/ppos.grpc.pb.cc")
set(ppos_grpc_hdrs "${CMAKE_CURRENT_BINARY_DIR}/ppos.grpc.pb.h")
add_custom_command(
      OUTPUT "${ppos_proto_srcs}" "${ppos_proto_hdrs}" "${ppos_grpc_srcs}" "${ppos_grpc_hdrs}"
      COMMAND ${_PROTOBUF_PROTOC}
      ARGS --grpc_out "${CMAKE_CURRENT_BINARY_DIR}"
        --cpp_out "${CMAKE_CURRENT_BINARY_DIR}"
        -I "${ppos_proto_path}"
        --plugin=protoc-gen-grpc="${_GRPC_CPP_PLUGIN_EXECUTABLE}"
        "${ppos_proto}"
      DEPENDS "${ppos_proto}" "${user_proto_hdrs}"
      COMMENT "Building ppos protos..."
)

#authentication proto
get_filename_component(authentication_proto "authentication.proto" ABSOLUTE)
get_filename_component(authentication_proto_path "${authentication_proto}" PATH)

set(authentication_proto_srcs "${CMAKE_CURRENT_BINARY_DIR}/authentication.pb.cc")
set(authentication_proto_hdrs "${CMAKE_CURRENT_BINARY_DIR}/authentication.pb.h")
set(authentication_grpc_srcs "${CMAKE_CURRENT_BINARY_DIR}/authentication.grpc.pb.cc")
set(authentication_grpc_hdrs "${CMAKE_CURRENT_BINARY_DIR}/authentication.grpc.pb.h")
add_custom_command(
      OUTPUT "${authentication_proto_srcs}" "${authentication_proto_hdrs}" "${authentication_grpc_srcs}" "${authentication_grpc_hdrs}"
      COMMAND ${_PROTOBUF_PROTOC}
      ARGS --grpc_out "${CMAKE_CURRENT_BINARY_DIR}"
        --cpp_out "${CMAKE_CURRENT_BINARY_DIR}"
        -I "${authentication_proto_path}"
        --plugin=protoc-gen-grpc="${_GRPC_CPP_PLUGIN_EXECUTABLE}"
        "${authentication_proto}"
      DEPENDS "${authentication_proto}"
      COMMENT "Building authentication protos..."
)


#display proto
get_filename_component(display_proto "display.proto" ABSOLUTE)
get_filename_component(display_proto_path "${display_proto}" PATH)

set(display_proto_srcs "${CMAKE_CURRENT_BINARY_DIR}/display.pb.cc")
set(display_proto_hdrs "${CMAKE_CURRENT_BINARY_DIR}/display.pb.h")
set(display_grpc_srcs "${CMAKE_CURRENT_BINARY_DIR}/display.grpc.pb.cc")
set(display_grpc_hdrs "${CMAKE_CURRENT_BINARY_DIR}/display.grpc.pb.h")
add_custom_command(
      OUTPUT "${display_proto_srcs}" "${display_proto_hdrs}" "${display_grpc_srcs}" "${display_grpc_hdrs}"
      COMMAND ${_PROTOBUF_PROTOC}
      ARGS --grpc_out "${CMAKE_CURRENT_BINARY_DIR}"
        --cpp_out "${CMAKE_CURRENT_BINARY_DIR}"
        -I "${display_proto_path}"
        --plugin=protoc-gen-grpc="${_GRPC_CPP_PLUGIN_EXECUTABLE}"
        "${display_proto}"
      DEPENDS "${display_proto}"
      COMMENT "Building display protos..."
)

#content proto
get_filename_component(content_proto "content.proto" ABSOLUTE)
get_filename_component(content_proto_path "${content_proto}" PATH)

set(content_proto_srcs "${CMAKE_CURRENT_BINARY_DIR}/content.pb.cc")
set(content_proto_hdrs "${CMAKE_CURRENT_BINARY_DIR}/content.pb.h")
set(content_grpc_srcs "${CMAKE_CURRENT_BINARY_DIR}/content.grpc.pb.cc")
set(content_grpc_hdrs "${CMAKE_CURRENT_BINARY_DIR}/content.grpc.pb.h")
add_custom_command(
      OUTPUT "${content_proto_srcs}" "${content_proto_hdrs}" "${content_grpc_srcs}" "${content_grpc_hdrs}"
      COMMAND ${_PROTOBUF_PROTOC}
      ARGS --grpc_out "${CMAKE_CURRENT_BINARY_DIR}"
        --cpp_out "${CMAKE_CURRENT_BINARY_DIR}"
        -I "${content_proto_path}"
        --plugin=protoc-gen-grpc="${_GRPC_CPP_PLUGIN_EXECUTABLE}"
        "${content_proto}"
      DEPENDS "${content_proto}"
      COMMENT "Building content protos..."
)


message("plugin = ${_GRPC_CPP_PLUGIN_EXECUTABLE}")
message("reflection= ${_REFLECTION}, grpc_grpcpp = ${_GRPC_GRPCPP}, protobuf_libprotobuf = ${_PROTOBUF_LIBPROTOBUF}")
include_directories("${CMAKE_CURRENT_BINARY_DIR}")


if (MSVC)
    set(WIN32FL WIN32)
    set(PCRE2_LIBRARIES "${VCPKG_ROOT}/installed/${VCPKG_TARGET_TRIPLET}/lib/pcre2-16.lib")
    SET(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} /MP /bigobj /D_UNICODE /DWIN32 /DNOMINMAX")
    add_compile_options(-DNO_PNG)
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    #set_property(GLOBAL PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
else()
	SET(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -fno-common -pedantic -pedantic-errors -Wall -Wextra -Werror \
  -Wno-deprecated-enum-enum-conversion -Wno-missing-field-initializers -Wmissing-include-dirs -Woverloaded-virtual -Wredundant-decls \
	-Wshadow -Wswitch-default -Wundef -Wno-switch-default -Wno-unused-parameter -Wno-shadow -Wno-unused-but-set-parameter")
endif()

#user protos -- must be generated first; since others refer to it.
set_source_files_properties(${user_grpc_srcs} ${user_grpc_hdrs} ${user_proto_srcs} ${user_proto_hdrs} PROPERTIES GENERATED TRUE)
add_library(user_grpc_proto ${user_grpc_srcs} ${user_grpc_hdrs} ${user_proto_srcs} ${user_proto_hdrs} ${user_proto})
target_link_libraries(user_grpc_proto ${_GRPC_GRPCPP} ${_PROTOBUF_LIBPROTOBUF})

#authentication protos
set_source_files_properties(${authentication_grpc_srcs} ${authentication_grpc_hdrs} ${authentication_proto_srcs} ${authentication_proto_hdrs} PROPERTIES GENERATED TRUE)
add_library(authentication_grpc_proto ${authentication_grpc_srcs} ${authentication_grpc_hdrs} ${authentication_proto_srcs} ${user_proto_hdrs} ${authentication_proto_hdrs} ${authentication_proto})
target_link_libraries(authentication_grpc_proto ${_GRPC_GRPCPP} ${_PROTOBUF_LIBPROTOBUF})

#display protos
set_source_files_properties(${display_grpc_srcs} ${display_grpc_hdrs} ${display_proto_srcs} ${display_proto_hdrs} PROPERTIES GENERATED TRUE)
add_library(display_grpc_proto ${display_grpc_srcs} ${display_grpc_hdrs} ${display_proto_srcs} ${display_proto_hdrs} ${user_proto_hdrs} ${display_proto})
target_link_libraries(display_grpc_proto ${_GRPC_GRPCPP} ${_PROTOBUF_LIBPROTOBUF})

#ppos protos
set_source_files_properties(${ppos_grpc_srcs} ${ppos_grpc_hdrs} ${ppos_proto_srcs} ${ppos_proto_hdrs} PROPERTIES GENERATED TRUE)
add_library(ppos_grpc_proto ${ppos_grpc_srcs} ${ppos_grpc_hdrs} ${ppos_proto_srcs} ${ppos_proto_hdrs} ${ppos_proto} ${user_proto_hdrs})
target_link_libraries(ppos_grpc_proto ${_GRPC_GRPCPP} ${_PROTOBUF_LIBPROTOBUF})

#content protos
set_source_files_properties(${content_grpc_srcs} ${content_grpc_hdrs} ${content_proto_srcs} ${content_proto_hdrs} PROPERTIES GENERATED TRUE)
add_library(content_grpc_proto ${content_grpc_srcs} ${content_grpc_hdrs} ${content_proto_srcs} ${content_proto_hdrs} ${user_proto_hdrs} ${content_proto})
target_link_libraries(content_grpc_proto ${_GRPC_GRPCPP} ${_PROTOBUF_LIBPROTOBUF})
