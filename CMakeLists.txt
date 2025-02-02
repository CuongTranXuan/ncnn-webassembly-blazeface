project(ncnn-webassembly-blazeface)

cmake_minimum_required(VERSION 3.10)

set(CMAKE_BUILD_TYPE release)


if(NOT WASM_FEATURE)
    message(FATAL_ERROR "You must pass cmake option -DWASM_FEATURE and possible values are basic, simd, threads and simd-threads")
endif()

set(ncnn_DIR "${CMAKE_CURRENT_SOURCE_DIR}/ncnn-20220216-webassembly/${WASM_FEATURE}/lib/cmake/ncnn")
find_package(ncnn REQUIRED)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -s FORCE_FILESYSTEM=1 -s INITIAL_MEMORY=256MB -s EXIT_RUNTIME=1")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -s FORCE_FILESYSTEM=1 -s INITIAL_MEMORY=256MB -s EXIT_RUNTIME=1")
set(CMAKE_EXECUTBLE_LINKER_FLAGS "${CMAKE_EXECUTBLE_LINKER_FLAGS} -s FORCE_FILESYSTEM=1 -s INITIAL_MEMORY=256MB -s EXIT_RUNTIME=1")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -sMODULARIZE=1 -sEXPORTED_FUNCTIONS=['_blazeface_ncnn'] --preload-file ${CMAKE_CURRENT_SOURCE_DIR}/assets@.")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sMODULARIZE=1 -sEXPORTED_FUNCTIONS=['_blazeface_ncnn'] --preload-file ${CMAKE_CURRENT_SOURCE_DIR}/assets@.")
set(CMAKE_EXECUTBLE_LINKER_FLAGS "${CMAKE_EXECUTBLE_LINKER_FLAGS}  -sMODULARIZE=1 -sEXPORTED_FUNCTIONS=['_blazeface_ncnn'] --preload-file ${CMAKE_CURRENT_SOURCE_DIR}/assets@.")

if(${WASM_FEATURE} MATCHES "threads")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -sEXPORT_NAME='blaze_ncnn_face' -fopenmp -pthread -s USE_PTHREADS=1 -s PTHREAD_POOL_SIZE=4")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -sEXPORT_NAME='blaze_ncnn_face' -fopenmp -pthread -s USE_PTHREADS=1 -s PTHREAD_POOL_SIZE=4")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -sEXPORT_NAME='blaze_ncnn_face' -fopenmp -pthread -s USE_PTHREADS=1 -s PTHREAD_POOL_SIZE=4")
endif()


add_executable(blazeface-${WASM_FEATURE} blazeface.cpp)
target_link_libraries(blazeface-${WASM_FEATURE} ncnn)
