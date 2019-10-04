cmake_minimum_required(VERSION 3.4.0)

#set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

#Glоb-ом не получится. В папке есть файлы не относящиеся к делу. 
#FILE(GLOB ZLib_src *.c)
#FILE(GLOB ZLib_h *.h)
#add_library(ZLib STATIC ${ZLib_src} ${ZLib_h})

add_library(
    zlib STATIC 
    adler32.c 
    compress.c 
    crc32.c 
    deflate.c 
    gzclose.c
    gzlib.c
    gzread.c
    gzwrite.c
    infback.c
    inffast.c
    inflate.c
    inftrees.c
    trees.c
    uncompr.c
    zutil.c
)
#На уровень выше - потому что так надо.
target_include_directories(zlib INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/../)

FixStaticLinking()

SET_TARGET_PROPERTIES (zlib PROPERTIES FOLDER Libraries)