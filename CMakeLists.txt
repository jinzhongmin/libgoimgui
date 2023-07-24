cmake_minimum_required(VERSION 3.1)
project(libgoimgui)

set (CMAKE_CXX_STANDARD 11)
if(WIN32) # to mingw work as all the others
	set(CMAKE_SHARED_LIBRARY_PREFIX "")
endif(WIN32)

# glfw
set(GLFW_INSTALL OFF)
set(GLFW_LIBRARY_TYPE "SHARED")
add_subdirectory(glfw)


# general settings
if (EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/cimgui/imgui/backends)
	set(BAKENDS_FOLDER "cimgui/imgui/backends/")
else()
	set(BAKENDS_FOLDER "cimgui/imgui/examples/")
endif()

if (EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/cimgui/imgui/imgui_tables.cpp)
	set(TABLES_SOURCE "cimgui/imgui/imgui_tables.cpp")
else()
	set(TABLES_SOURCE "")
endif()

include_directories(cimgui/imgui)
add_definitions("-DIMGUI_DISABLE_OBSOLETE_FUNCTIONS=1")

include_directories(cimgui)
set(IMGUI_SOURCES 
	cimgui/cimgui.cpp 
	cimgui/imgui/imgui.cpp 
	cimgui/imgui/imgui_draw.cpp 
	cimgui/imgui/imgui_demo.cpp 
	cimgui/imgui/imgui_widgets.cpp
	${TABLES_SOURCE} 
)

set(IMGUI_SOURCES_sdl)
set(IMGUI_LIBRARIES )

if (WIN32)
    add_definitions("-DIMGUI_IMPL_API=extern \"C\" __declspec\(dllexport\)")
else(WIN32)
    add_definitions("-DIMGUI_IMPL_API=extern \"C\" ")
endif(WIN32)

add_compile_definitions("IMGUI_IMPL_OPENGL_LOADER_GL3W")

# optional adding freetype
option(IMGUI_FREETYPE "add Freetype2" OFF)

if(IMGUI_FREETYPE)
	FIND_PACKAGE(freetype REQUIRED PATHS ${FREETYPE_PATH})
	list(APPEND IMGUI_LIBRARIES freetype)
	list(APPEND IMGUI_SOURCES cimgui/imgui/misc/freetype/imgui_freetype.cpp)
	add_definitions("-DCIMGUI_FREETYPE=1")
endif(IMGUI_FREETYPE)

# opengl3
list(APPEND IMGUI_SOURCES ${BAKENDS_FOLDER}imgui_impl_opengl3.cpp)
include_directories(cimgui/imgui/examples/libs/gl3w)

#if(WIN32)
#        list(APPEND IMGUI_LIBRARIES opengl32)
# else(WIN32) # Unix
#        list(APPEND IMGUI_LIBRARIES GL)
# endif(WIN32)
list(APPEND IMGUI_LIBRARIES glfw)

# GLFW
list(APPEND IMGUI_SOURCES ${BAKENDS_FOLDER}imgui_impl_glfw.cpp)

# MINGW AND WIN32
set(IMGUI_LINK_FLAGS )
if(MINGW AND WIN32)
	list(APPEND IMGUI_LINK_FLAGS -static-libgcc;-static-libstdc++ -Wl,-Bstatic,--whole-archive -lwinpthread -Wl,--no-whole-archive
)
endif(MINGW AND WIN32)
# message(STATUS ${IMGUI_LINK_FLAGS})

add_library(cimgui SHARED ${IMGUI_SOURCES})
target_link_libraries(cimgui ${IMGUI_LIBRARIES} ${IMGUI_LINK_FLAGS})


install(FILES 
	${CMAKE_BINARY_DIR}/glfw/src/glfw3${CMAKE_SHARED_LIBRARY_SUFFIX}
	DESTINATION .)
install(TARGETS cimgui
    RUNTIME DESTINATION  .
    LIBRARY DESTINATION  .
    ARCHIVE DESTINATION  .
)