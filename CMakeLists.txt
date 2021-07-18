cmake_minimum_required(VERSION 3.20)
project(vslsmas C)

find_package(Git REQUIRED)
execute_process(
    COMMAND ${GIT_EXECUTABLE} describe --tags --long
    WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}"
    OUTPUT_VARIABLE VCS_TAG)
string(STRIP ${VCS_TAG} VCS_TAG)
configure_file(VapourSynth/version.h.in version.h)

find_package(FFMPEG 4.4 REQUIRED)
find_package(zstd 1.4 REQUIRED)
find_package(xxHash 0.8 REQUIRED)

set(CMAKE_C_STANDARD 99 REQUIRED)
set(CMAKE_C_VISIBILITY_PRESET hidden)
set(DEFAULT_CACHEDIR "\"\"" CACHE STRING "Default value of cachedir")

add_library(vslsmas SHARED
    "VapourSynth/lsmashsource.c"
    "VapourSynth/lsmashsource.h"
    "VapourSynth/lwlibav_source.c"
    "VapourSynth/video_output.c"
    "VapourSynth/video_output.h"
    "common/decode.c"
    "common/decode.h"
    "common/lwindex.c"
    "common/lwindex.h"
    "common/lwlibav_audio.c"
    "common/lwlibav_audio.h"
    "common/lwlibav_dec.c"
    "common/lwlibav_dec.h"
    "common/lwlibav_video.c"
    "common/lwlibav_video.h"
    "common/osdep.c"
    "common/osdep.h"
    "common/qsv.c"
    "common/qsv.h"
    "common/utils.c"
    "common/utils.h"
    "common/video_output.c"
    "common/video_output.h")
target_include_directories(vslsmas PRIVATE
    "${PROJECT_SOURCE_DIR}/include"
    "${PROJECT_BINARY_DIR}"
    ${FFMPEG_INCLUDE_DIRS})
target_link_directories(vslsmas PRIVATE
    ${FFMPEG_LIBRARY_DIRS})
target_link_libraries(vslsmas PRIVATE
    ${FFMPEG_LIBRARIES}
    zstd::libzstd_static)
target_compile_definitions(vslsmas PRIVATE
    "DEFAULT_CACHEDIR=${DEFAULT_CACHEDIR}"
    XXH_INLINE_ALL
    "$<$<C_COMPILER_ID:MSVC>:_CRT_DISABLE_PERFCRIT_LOCKS>")
target_compile_options(vslsmas PRIVATE
    "$<$<C_COMPILER_ID:MSVC>:/utf-8>"
    "$<$<C_COMPILER_ID:MSVC>:/arch:AVX2>")
