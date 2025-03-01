# Copyright (C) 2018-2021 Intel Corporation
# SPDX-License-Identifier: Apache-2.0
#

set(TARGET_NAME "inference_engine_ir_v7_reader")

file(GLOB_RECURSE LIBRARY_SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp
                              ${CMAKE_CURRENT_SOURCE_DIR}/*.h
                              ${CMAKE_CURRENT_SOURCE_DIR}/*.hpp)

# Create named folders for the sources within the .vcproj
# Empty name lists them directly under the .vcproj

source_group("src" FILES ${LIBRARY_SRC})

# Create module library

if(BUILD_SHARED_LIBS)
    set(library_type MODULE)
else()
    set(library_type STATIC)
endif()

add_library(${TARGET_NAME} ${library_type} ${LIBRARY_SRC})

ie_faster_build(${TARGET_NAME}
    UNITY
)

ie_add_vs_version_file(NAME ${TARGET_NAME}
                       FILEDESCRIPTION "Inference Engine IR v7 reader plugin")

target_compile_definitions(${TARGET_NAME} PRIVATE IMPLEMENT_INFERENCE_ENGINE_PLUGIN)

target_include_directories(${TARGET_NAME} PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/")

target_link_libraries(${TARGET_NAME} PRIVATE openvino::runtime::dev
                                             inference_engine_legacy pugixml::static openvino::itt)

ie_add_api_validator_post_build_step(TARGET ${TARGET_NAME})

set_target_properties(${TARGET_NAME} PROPERTIES INTERPROCEDURAL_OPTIMIZATION_RELEASE ${ENABLE_LTO})

if(WIN32)
    set_target_properties(${TARGET_NAME} PROPERTIES COMPILE_PDB_NAME ${TARGET_NAME})
endif()

if(BUILD_SHARED_LIBS)
    target_link_libraries(${TARGET_NAME} PRIVATE inference_engine)
else()
    # for static linkage the dependencies are in opposite order
    target_link_libraries(ov_runtime PRIVATE ${TARGET_NAME})
endif()

# code style

add_cpplint_target(${TARGET_NAME}_cpplint FOR_TARGETS ${TARGET_NAME})

# Install rules

# for ieFuncTests
install(TARGETS ${TARGET_NAME}
        LIBRARY DESTINATION ${IE_CPACK_RUNTIME_PATH} COMPONENT tests EXCLUDE_FROM_ALL)

# for InferenceEngineUnitTest
# For public CI
install(TARGETS ${TARGET_NAME}
    LIBRARY DESTINATION tests COMPONENT tests EXCLUDE_FROM_ALL)
if(NOT WIN32)
    # For private CI
    install(TARGETS ${TARGET_NAME}
            LIBRARY DESTINATION tests/lib COMPONENT tests EXCLUDE_FROM_ALL)
endif()

ov_install_static_lib(${TARGET_NAME} core)
