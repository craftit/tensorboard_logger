cmake_minimum_required(VERSION 3.10)
project(tensorboard_logger)

set(CMAKE_CXX_STANDARD 17)

find_package(Protobuf REQUIRED)

include_directories(${Protobuf_INCLUDE_DIRS})
include_directories(${CMAKE_CURRENT_BINARY_DIR})

protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS proto/event.proto proto/projector_config.proto proto/resource_handle.proto
        proto/summary.proto  proto/tensor.proto proto/tensor_shape.proto proto/types.proto)

# Note: Use the command ``make proto`` to generate protobuf C++ files before using this CMake script
add_library(tensorboard_logger src/crc.cc src/tensorboard_logger.cc ${PROTO_SRCS} ${PROTO_HDRS})
target_include_directories(tensorboard_logger PUBLIC include)
target_link_libraries(tensorboard_logger PUBLIC protobuf::libprotobuf-lite)
target_link_libraries(tensorboard_logger PUBLIC ${Protobuf_LIBRARIES})

add_executable(test_tensorboard_logger tests/test_tensorboard_logger.cc )
target_link_libraries(test_tensorboard_logger tensorboard_logger)