cmake_minimum_required(VERSION 3.4.0)
#Алярма. Я не утверждаю, что мой способ - самый правильный. Скорее наоборот. 
#Но он замечательно копипастится в другие проекты. И относительно удобен.

project(amxxpc)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

set (CMAKE_CXX_STANDARD 11)

#Ниже немного магии

#Во-первых, определим тип платформы. (32 или 64 бита)
if(NOT "${CMAKE_SIZEOF_VOID_P}" STREQUAL "4" AND NOT "${CMAKE_SIZEOF_VOID_P}" STREQUAL "8")
	message(FATAL_ERROR "CMAKE_SIZEOF_VOID_P=${CMAKE_SIZEOF_VOID_P}. Unknown platform")
else()
	if("${CMAKE_SIZEOF_VOID_P}" STREQUAL "4")
		add_compile_definitions(__PLAT_TYPE_32__)
		set(PLATFORM_TYPE 32)
	else()
		add_compile_definitions(__PLAT_TYPE_64__)
		set(PLATFORM_TYPE 64)
	endif()
endif()

#И имя платформы. Шоб было.
if(WIN32)
	add_compile_definitions(_CRT_SECURE_NO_WARNINGS)
	if("${PLATFORM_TYPE}" STREQUAL "32")
		set(PLATFOTM_NAME win32)
	endif()

	if("${PLATFORM_TYPE}" STREQUAL "64")
		set(PLATFOTM_NAME x64)
	endif()
else()
	#Тут надо бы что-то для других платформ. Ну да ладно.
	set(PLATFOTM_NAME "")
endif()


macro(FixStaticLinking)
#Этот макрос нам нужен для статической линковки CTR под виндами. (Чтобы всякие dll-ки не тащились)
if(MSVC)
	foreach(flag
			CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
			CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
		if(${flag} MATCHES "/MD")
			string(REGEX REPLACE "/MD" "/MT" ${flag} "${${flag}}")
		endif()
	endforeach()
endif()
endmacro()

macro(PrintDirs var)
	#Сейчас не нужен, но как пример - пусть будет.
	get_property(${var}_LIBRARY_OUTPUT_DIRECTORY TARGET ${var} PROPERTY LIBRARY_OUTPUT_DIRECTORY)
	message(${var}_LIBRARY_OUTPUT_DIRECTORY=${${var}_LIBRARY_OUTPUT_DIRECTORY})
	message(${var}_COMPILE_PDB_OUTPUT_DIRECTORY=${COMPILE_PDB_OUTPUT_DIRECTORY})
endmacro()

macro(FixExePostfix Target)
	#Этот макрос нужен для изменения постфикса .exe-шки. CMAKE_<CONFIG>_POSTFIX на неё не действует.
	set_target_properties(${Target} PROPERTIES DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX})
	set_target_properties(${Target} PROPERTIES RELEASE_POSTFIX ${CMAKE_RELEASE_POSTFIX})
	set_target_properties(${Target} PROPERTIES MINSIZEREL_POSTFIX ${CMAKE_MINSIZEREL_POSTFIX})
	set_target_properties(${Target} PROPERTIES RELWITHDEBINFO_POSTFIX ${CMAKE_RELWITHDEBINFO_POSTFIX})
endmacro()

#Назначим директории, куда будут складываться результаты, в зависимости от конфгурации.
if(CMAKE_CONFIGURATION_TYPES)
	set(CMAKE_DEBUG_POSTFIX "_${PLATFORM_TYPE}_d")
	set(CMAKE_RELEASE_POSTFIX "_${PLATFORM_TYPE}")
	set(CMAKE_MINSIZEREL_POSTFIX "_${PLATFORM_TYPE}_min")
	set(CMAKE_RELWITHDEBINFO_POSTFIX "_${PLATFORM_TYPE}_rd")
	
	foreach(cfg ${CMAKE_CONFIGURATION_TYPES})
		string(TOUPPER ${cfg} cfgup)
		set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_${cfgup} ${CMAKE_BINARY_DIR}/_Output/Lib)
		set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_${cfgup} ${CMAKE_BINARY_DIR}/_Output/Lib)
		set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${cfgup} ${CMAKE_BINARY_DIR}/_Output/Bin)
	endforeach()
else()
	#Если конфигураций нет (бывает такое), то всё равно что-то назначим, чтобы потом не собирать всё руками.
		set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/_Output/${PLATFOTM_NAME}/Lib)
		set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/_Output/${PLATFOTM_NAME}/Lib)
		set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/_Output/${PLATFOTM_NAME}/Bin)	
endif()



add_subdirectory(public)
add_subdirectory(shared)
add_subdirectory(src)