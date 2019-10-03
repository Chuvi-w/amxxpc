cmake_minimum_required(VERSION 3.4.0)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "build")
set(LIBRARY_OUTPUT_PATH "build")
set(EXECUTABLE_OUTPUT_PATH "build")

project(amxxpc)
set (CMAKE_CXX_STANDARD 11)

link_directories(${CMAKE_CURRENT_BINARY_DIR})

set(ZLIB shared/zlib)
set(LIBPC300 shared/libpc300)
set(SRC src)

add_library(
    zlib STATIC 
    ${ZLIB}/adler32.c 
    ${ZLIB}/compress.c 
    ${ZLIB}/crc32.c 
    ${ZLIB}/deflate.c 
    ${ZLIB}/gzclose.c
    ${ZLIB}/gzlib.c
    ${ZLIB}/gzread.c
    ${ZLIB}/gzwrite.c
    ${ZLIB}/infback.c
    ${ZLIB}/inffast.c
    ${ZLIB}/inflate.c
    ${ZLIB}/inftrees.c
    ${ZLIB}/trees.c
    ${ZLIB}/uncompr.c
    ${ZLIB}/zutil.c
)
target_compile_definitions(zlib PUBLIC HAVE_UNISTD_H)
set_target_properties(zlib PROPERTIES PREFIX "")
set_target_properties(zlib PROPERTIES COMPILE_FLAGS "-m32" LINK_FLAGS "-m32")

add_library(
    amxxpc32 SHARED
    ${LIBPC300}/sc1.c
    ${LIBPC300}/sc2.c
    ${LIBPC300}/sc3.c
    ${LIBPC300}/sc4.c
    ${LIBPC300}/sc5.c
    ${LIBPC300}/sc6.c
    ${LIBPC300}/sc7.c
    ${LIBPC300}/scvars.c
    ${LIBPC300}/scmemfil.c
    ${LIBPC300}/scstate.c
    ${LIBPC300}/sclist.c
    ${LIBPC300}/sci18n.c
    ${LIBPC300}/pawncc.c
    ${LIBPC300}/libpawnc.c
    ${LIBPC300}/prefix.c
    ${LIBPC300}/memfile.c
    ${LIBPC300}/sp_symhash.c
)
target_compile_definitions(amxxpc32 PRIVATE NO_MAIN PRIVATE PAWNC_DLL PRIVATE HAVE_STDINT_H PRIVATE _GNU_SOURCE PRIVATE ENABLE_BINRELOC)
target_compile_definitions(amxxpc32 PRIVATE PAWN_CELL_SIZE=32 PRIVATE _LINUX PRIVATE POSIX PRIVATE LINUX)
set_target_properties(amxxpc32 PROPERTIES PREFIX "")
set_target_properties(amxxpc32 PROPERTIES COMPILE_FLAGS "-lm -pthread -m32" LINK_FLAGS "-lm -pthread -m32")
target_include_directories(amxxpc32 PRIVATE ${LIBPC300})

add_executable(${PROJECT_NAME} ${SRC}/amx.cpp ${SRC}/amxxpc.cpp ${SRC}/Binary.cpp)
target_compile_definitions(${PROJECT_NAME} PRIVATE PAWN_CELL_SIZE=32 PRIVATE _LINUX PRIVATE POSIX PRIVATE LINUX PRIVATE AMX_ANSIONLY)
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "-m32 -ldl -lm -static-libgcc" LINK_FLAGS "-m32 -ldl -lm -static-libgcc")
target_include_directories(${PROJECT_NAME} PRIVATE ${SRC} PRIVATE shared  PRIVATE public)
target_link_libraries(${PROJECT_NAME} zlib)
