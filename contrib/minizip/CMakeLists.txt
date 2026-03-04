
set(LIBMINIZIP_SRCS ioapi.c mztools.c unzip.c zip.c)

set(LIBMINIZIP_HDRS crypt.h ints.h ioapi.h mztools.h unzip.h zip.h)

set(MINIZIP_HDRS crypt.h ints.h ioapi.h iowin32.h skipset.h zip.h)

set(MINIUNZIP_SRCS ioapi.c iowin32.c miniunz.c unzip.c zip.c)

set(MINIUNZIP_HDRS
    crypt.h
    ints.h
    ioapi.h
    iowin32.h
    skipset.h
    unzip.h
    zip.h)

set(minizip_static_suffix "s")
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)

add_library(libminizip SHARED
            ${LIBMINIZIP_SRCS}
            ${LIBMINIZIP_HDRS})
add_library(MINIZIP::minizip ALIAS libminizip)
target_include_directories(libminizip PUBLIC ${minizip_SOURCE_DIR})
target_compile_definitions(libminizip
    PRIVATE
        _CRT_SECURE_NO_WARNINGS
        $<$<BOOL:${HAVE___ATTR__VIS_HIDDEN}>:HAVE_HIDDEN>
        $<$<BOOL:${HAVE_UNISTD_H}>:HAVE_UNISTD_H=1>
    PUBLIC
        $<$<BOOL:${HAVE_OFF64_T}>:_LARGEFILE64_SOURCE=1>
        $<$<BOOL:NOT:${HAVE_FOPEN64}>:USE_FILE32API=1>)

set_target_properties(libminizip
    PROPERTIES
        SOVERSION ${zlib_VERSION_MAJOR}
        VERSION ${INSTALL_VERSION}
        OUTPUT_NAME libminizip)
target_link_libraries(libminizip PUBLIC ZLIB::ZLIB)

add_executable(minizip1
    ioapi.c iowin32.c minizip.c zip.c
    ${MINIZIP_HDRS})
set_target_properties(minizip1 PROPERTIES OUTPUT_NAME minizip1)
target_compile_definitions(minizip1 PRIVATE _CRT_SECURE_NO_WARNINGS)
target_link_libraries(minizip1 PRIVATE MINIZIP::minizip ZLIB1DLL::ZLIB1DLL)

add_executable(minizip2
    ioapi.c iowin32.c minizip2.c zip.c
    ${MINIZIP_HDRS})
set_target_properties(minizip2 PROPERTIES OUTPUT_NAME minizip2)
target_compile_definitions(minizip2 PRIVATE _CRT_SECURE_NO_WARNINGS)
target_link_libraries(minizip2 PRIVATE MINIZIP::minizip)

add_test(NAME mz1
    COMMAND minizip1 test1.zip ${CMAKE_CURRENT_SOURCE_DIR}/test.txt)

set_tests_properties(mz1 PROPERTIES ENVIRONMENT "PATH=$<TARGET_FILE_DIR:zlib>\;$<TARGET_FILE_DIR:zlib1>\;$ENV{PATH}")

add_test(NAME mz2
    COMMAND minizip2 test2.zip ${CMAKE_CURRENT_SOURCE_DIR}/test.txt)

set_tests_properties(mz2 PROPERTIES ENVIRONMENT "PATH=$<TARGET_FILE_DIR:zlib>\;$<TARGET_FILE_DIR:zlib1>\;$ENV{PATH}")