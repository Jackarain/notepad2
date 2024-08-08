cmake_minimum_required(VERSION 3.16)
project(scintilla)

file(GLOB scintilla_SRC
	lexers/*.cxx
	lexlib/*.cxx
	src/*.cxx
	win32/*.cxx

	lexers/*.h
	lexlib/*.h
	src/*.h
	win32/*.h
)

add_library(scintilla STATIC ${scintilla_SRC})

target_include_directories(scintilla
	PUBLIC
	${CMAKE_CURRENT_SOURCE_DIR}/include
	${CMAKE_CURRENT_SOURCE_DIR}/lexlib
	${CMAKE_CURRENT_SOURCE_DIR}/src
)

target_compile_definitions(scintilla
	PUBLIC
	-DUNICODE
	-D_UNICODE
)

set(SCINTILLA_INCLUDE_DIRS
	${CMAKE_CURRENT_SOURCE_DIR}/include
	PARENT_SCOPE
)

set(SCINTILLA_LIBRARIES
	scintilla
	PARENT_SCOPE
)
