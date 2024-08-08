cmake_minimum_required(VERSION 3.5)
cmake_policy(SET CMP0091 NEW) # for CMAKE_MSVC_RUNTIME_LIBRARY
set(CMAKE_POLICY_DEFAULT_CMP0077 NEW) # cmake_policy(SET CMP0077 NEW)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
project(notepad4 LANGUAGES CXX)

include(CheckCXXCompilerFlag)
include(CheckLibraryExists)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if (MINGW)
    message(STATUS "****************************** MINGW ******************************")

    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static -static-libstdc++")

    add_compile_options(-mwindows -municode -mthreads)
    add_link_options(-mwindows -municode)

    if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        # 在这里，需要将 src/Notepad4.rc 中的路径 \\ 替换为 linux 支持的路径 /
        # 可以使用命令 sed -i 's#\\\\#/#g' Notepad4.rc 来替换
        # 否则有可能找不到 res 目录下的资源文件
    endif()
endif()

add_definitions(
    -DWIN32_LEAN_AND_MEAN
    -D_WIN32_WINNT=0x0501
    -DUNICODE
    -D_UNICODE
)

add_subdirectory(scintilla)

file(GLOB notepad4_SRC
	src/*.c
	src/*.cxx
    src/*.cpp
    src/*.rc

    src/EditLexers/*.cpp

	src/*.h
    src/EditLexers/*.h
)

add_executable(notepad4
    WIN32
    ${notepad4_SRC}
)

target_link_libraries(notepad4
    ${SCINTILLA_LIBRARIES}

    shlwapi
    ole32
    imm32
    gdi32
    comdlg32
    comctl32
    uxtheme
    oleaut32
    uuid
)

target_include_directories(notepad4
    PUBLIC
    ${SCINTILLA_INCLUDE_DIRS}
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)
