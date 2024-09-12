# https://github.com/microsoft/vcpkg/tree/b4624c3a701b11248d88aab08744a37ee7aea1cc/ports/nanovg

cmake_minimum_required(VERSION 3.11)
project(nanovg C)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED TRUE)

set(THIRDPARTY_HEADERS thirdparty/stb_image.h thirdparty/stb_truetype.h)

set(NANOVG_SOURCES src/nanovg.c)
set(NANOVG_HEADERS src/nanovg.h src/nanovg_gl.h src/nanovg_gl_utils.h src/fontstash.h)

add_library(nanovg STATIC ${NANOVG_SOURCES} ${NANOVG_HEADERS})
set_target_properties(nanovg PROPERTIES PUBLIC_HEADER "${NANOVG_HEADERS}")
target_include_directories(nanovg PRIVATE thirdparty)

if (CMAKE_BUILD_TYPE STREQUAL "Debug")
	target_compile_definitions(nanovg PRIVATE DEBUG)
else ()
	target_compile_definitions(nanovg PRIVATE NDEBUG)
endif ()

add_executable(playground playground/main.c playground/glad.c)
target_include_directories(playground PRIVATE playground src thirdparty)
target_link_libraries(playground PRIVATE nanovg)

#[[
install(TARGETS nanovg
		EXPORT nanovgTargets
		RUNTIME DESTINATION bin
		ARCHIVE DESTINATION lib
		LIBRARY DESTINATION lib
		PUBLIC_HEADER DESTINATION include)

install(EXPORT nanovgTargets
		FILE nanovgTargets.cmake
		NAMESPACE nanovg::
		DESTINATION share/nanovg)

install(FILES
		${CMAKE_CURRENT_SOURCE_DIR}/nanovgConfig.cmake
		DESTINATION share/nanovg)]]
