# Copyright (C) 2018-2021 Intel Corporation
# SPDX-License-Identifier: Apache-2.0
#

if(CMAKE_COMPILER_IS_GNUCXX)
    ie_add_compiler_flags(-Wall)
    ie_add_compiler_flags(-Wmissing-declarations)
endif()

if(ENABLE_VPU)
    add_subdirectory(vpu)
endif()
