cmake_minimum_required(VERSION 3.10)
project(notepad4 LANGUAGES CXX)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_STANDARD 20)

set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

if (MSVC)
    set(CompilerFlags
            CMAKE_CXX_FLAGS
            CMAKE_CXX_FLAGS_DEBUG
            CMAKE_CXX_FLAGS_RELEASE
            CMAKE_CXX_FLAGS_MINSIZEREL
            CMAKE_CXX_FLAGS_RELWITHDEBINFO
            CMAKE_C_FLAGS
            CMAKE_C_FLAGS_DEBUG
            CMAKE_C_FLAGS_RELEASE
            CMAKE_C_FLAGS_MINSIZEREL
            CMAKE_C_FLAGS_RELWITHDEBINFO
            )
    foreach(CompilerFlag ${CompilerFlags})
        string(REPLACE "/MD" "/MT" ${CompilerFlag} "${${CompilerFlag}}")
        set(${CompilerFlag} "${${CompilerFlag}}" CACHE STRING "msvc compiler flags" FORCE)
        message("MSVC flags: ${CompilerFlag}:${${CompilerFlag}}")
    endforeach()
endif()

if (MINGW)
    message(STATUS "*** Using MINGW ***")

    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static -static-libstdc++")

    add_compile_options(-mwindows -municode -mthreads)
    add_link_options(-mwindows -municode)

    if (${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        # 在这里，需要将 src/Notepad4.rc 中的路径 \\ 替换为 linux 支持的路径 /
        # 可以使用命令 sed -i 's#\\\\#/#g' Notepad4.rc 来替换
        # 否则有可能找不到 res 目录下的资源文件
    endif()
endif()

add_definitions(
	-D_CRT_SECURE_NO_WARNINGS

    -DWIN32_LEAN_AND_MEAN
    -D_WIN32_WINNT=0x0600
    -DNOMINMAX
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
    src/DarkMode/*.cpp

    src/*.h

    src/EditLexers/*.h
    src/DarkMode/*.h
)

add_executable(notepad4
    WIN32
    ${notepad4_SRC}
)

if (MSVC)
    target_link_options(notepad4 PRIVATE /MANIFEST:NO)
endif()

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
	dwmapi
)

target_include_directories(notepad4
    PUBLIC
    ${SCINTILLA_INCLUDE_DIRS}
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

install(TARGETS notepad4 DESTINATION bin)
