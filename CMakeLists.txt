cmake_minimum_required(VERSION 2.8)
project(liboca C)

include(cmake/CheckCCompilerFlag.txt)
if(WIN32)
	function(add_lint SOURCES_LIST)
		message(STATUS "Linting not supported on WINx platforms")
	endfunction()
else()
	include(cmake/Lint.txt)
endif()

if(${CMAKE_BUILD_TYPE} MATCHES "Debug")
	add_cflag_if_supported("-O0")
	add_cflag_if_supported("-ggdb")
	add_cflag_if_supported("-fprofile-arcs")
	add_cflag_if_supported("-ftest-coverage")

	add_definitions("-DDEBUG")

	include(CTest)
elseif()
	add_cflag_if_supported("-O2")

	add_definitions("-DRELEASE")
endif()

set(SRC src/oca.c)
include_directories(include)

if(OCA_LINT AND BUILD_TESTING)
	add_lint(${SRC})
endif()

add_library(${PROJECT_NAME}-static STATIC ${SRC})
set_target_properties(${PROJECT_NAME}-static PROPERTIES OUTPUT_NAME ${PROJECT_NAME} PREFIX "")

add_library(${PROJECT_NAME}-shared SHARED ${SRC})
set_target_properties(${PROJECT_NAME}-shared PROPERTIES OUTPUT_NAME ${PROJECT_NAME} PREFIX "")

add_subdirectory("tests")
