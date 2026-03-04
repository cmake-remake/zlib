# ============================================================================
# zlib1-dll
# ============================================================================
set(ZLIB1-DLL_PUBLIC_HDRS
    ${ZCONF_H}
    ../../zlib.h
    ../minizip/crypt.h
    ../minizip/ints.h
    ../minizip/ioapi.h
    ../minizip/mztools.h
    ../minizip/unzip.h
    ../minizip/zip.h)

set(ZLIB1-DLL_PRIVATE_HDRS
    ../../crc32.h
    ../../deflate.h
    ../../gzguts.h
    ../../inffast.h
    ../../inffixed.h
    ../../inflate.h
    ../../inftrees.h
    ../../trees.h
    ../../zutil.h)

set(ZLIB1-DLL_SRCS
    ../../adler32.c
    ../../compress.c
    ../../crc32.c
    ../../deflate.c
    ../../gzclose.c
    ../../gzlib.c
    ../../gzread.c
    ../../gzwrite.c
    ../../inflate.c
    ../../infback.c
    ../../inftrees.c
    ../../inffast.c
    ../../trees.c
    ../../uncompr.c
    ../../zutil.c
    ../minizip/ioapi.c
    ../minizip/mztools.c
    ../minizip/unzip.c
    ../minizip/zip.c)

cmake_path(SET p1 "../../")
cmake_path(SET p2 "../../")
cmake_path(ABSOLUTE_PATH "p1" OUTPUT_VARIABLE DOTDOT)
cmake_path(ABSOLUTE_PATH "p2" OUTPUT_VARIABLE DOTMINIZIP)

add_library(zlib1 SHARED
    ${ZLIB1-DLL_SRCS}
    ${ZLIB1-DLL_PUBLIC_HDRS}
    ${ZLIB1-DLL_PRIVATE_HDRS}
    zlib1.rc)

add_library(ZLIB1DLL::ZLIB1DLL ALIAS zlib1)

set_target_properties(zlib1 PROPERTIES OUTPUT_NAME zlib1)

target_compile_definitions(zlib1
    PRIVATE ZLIB_BUILD
            $<$<BOOL:NOT:${HAVE_FSEEKO}>:NO_FSEEKO>
            $<$<BOOL:${HAVE_UNISTD_H}>:HAVE_UNISTD_H=1>
            $<$<BOOL:${HAVE___ATTR__VIS_HIDDEN}>:HAVE_HIDDEN>
            _CRT_SECURE_NO_DEPRECATE
            _CRT_NONSTDC_NO_DEPRECATE
PUBLIC $<$<BOOL:${HAVE_OFF64_T}>:_LARGEFILE64_SOURCE=1>
       $<$<BOOL:NOT:${HAVE_FOPEN64}>:USE_FILE32API=1>)

target_include_directories(zlib1
    PUBLIC ${DOTDOT}
           ${DOTMINIZIP}
           ${CMAKE_CURRENT_BINARY_DIR})

set_target_properties(zlib1 PROPERTIES DEFINE_SYMBOL ZLIB_DLL)