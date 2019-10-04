cmake_minimum_required(VERSION 3.4)

FILE(GLOB AmxxPC_src *.cpp)
FILE(GLOB AmxxPC_h *.h)

#set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
add_executable(amxxpc ${AmxxPC_src} ${AmxxPC_h})

target_link_libraries(amxxpc AmxxVersion zlib)
#FixStaticLinking()

