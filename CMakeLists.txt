cmake_minimum_required(VERSION 3.19.0)
project(ObjLoader)

set(ObjLoaderBuildExamples OFF CACHE BOOL "If enabled, examples for OBJ Loader will be built.")

set(INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)
add_library(ObjLoader INTERFACE ${INCLUDE_DIR}/OBJ_Loader/OBJ_Loader.h)
target_include_directories(ObjLoader INTERFACE ${INCLUDE_DIR})

set(EXAMPLES_DIR ${CMAKE_CURRENT_SOURCE_DIR}/examples)
macro(add_example TARGET_NAME)
    add_executable(${TARGET_NAME})
    target_link_libraries(${TARGET_NAME} PRIVATE ObjLoader)
    foreach(FILE ${ARGN})
        get_filename_component(EXTENSION ${FILE} EXT)
        if (${EXTENSION} STREQUAL ".cpp" OR ${EXTENSION} STREQUAL ".h")
            target_sources(${TARGET_NAME} PRIVATE ${ARGN})
        else()
            get_filename_component(FILE_NAME ${FILE} NAME)
            set(OUTPUT_PATH ${CMAKE_BINARY_DIR}/${FILE_NAME})
            add_custom_command(
                OUTPUT "${OUTPUT_PATH}"
                COMMAND ${CMAKE_COMMAND} ARGS -E copy "${FILE}" "${OUTPUT_PATH}"
                MAIN_DEPENDENCY "${FILE}"
                DEPENDS "${FILE}"
                WORKING_DIRECTORY "${CMAKE_BINARY_DIR}"
                COMMENT "Copying ${FILE} to ${OUTPUT_PATH}"
            )
        endif()
    endforeach()
endmacro()

if(ObjLoaderBuildExamples)
    add_example(LoadAndPrint
        "${EXAMPLES_DIR}/1 - LoadAndPrint/e1_loadandprint.cpp"
        "${EXAMPLES_DIR}/1 - LoadAndPrint/box_stack.mtl"
        "${EXAMPLES_DIR}/1 - LoadAndPrint/box_stack.obj"
    )
endif()
