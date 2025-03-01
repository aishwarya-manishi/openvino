# Copyright (C) 2018-2021 Intel Corporation
# SPDX-License-Identifier: Apache-2.0
#

set(TARGET_NAME "onnx_frontend_tests")

file(GLOB_RECURSE SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

add_executable(${TARGET_NAME} ${SRC})

target_link_libraries(${TARGET_NAME} PRIVATE frontend_shared_test_classes)

add_clang_format_target(${TARGET_NAME}_clang FOR_TARGETS ${TARGET_NAME})

install(TARGETS ${TARGET_NAME}
        RUNTIME DESTINATION tests
        COMPONENT tests
        EXCLUDE_FROM_ALL)

set(TEST_ONNX_MODELS_DIRNAME ${TEST_MODEL_ZOO}/src/core/models/onnx)
target_compile_definitions(${TARGET_NAME} PRIVATE -D TEST_ONNX_MODELS_DIRNAME=\"${TEST_ONNX_MODELS_DIRNAME}/\")
set(MANIFEST ${CMAKE_CURRENT_SOURCE_DIR}/unit_test.manifest)
target_compile_definitions(${TARGET_NAME} PRIVATE -D MANIFEST=\"${MANIFEST}\")

add_dependencies(${TARGET_NAME} ov_onnx_frontend)
add_dependencies(${TARGET_NAME} test_model_zoo)
