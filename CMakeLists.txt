# CMakeList.txt : CMake project for Ectoplasme, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.8)
project(Magma VERSION 0.1.0 LANGUAGES CXX C)

#possible rendering api : VULKAN and OPENGL
set(RENDERING_API VULKAN)

include(FetchContent)

# Enable Hot Reload for MSVC compilers if supported.
if(POLICY CMP0141)
  cmake_policy(SET CMP0141 NEW)
  set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT "$<IF:$<AND:$<C_COMPILER_ID:MSVC>,$<CXX_COMPILER_ID:MSVC>>,$<$<CONFIG:Debug,RelWithDebInfo>:EditAndContinue>,$<$<CONFIG:Debug,RelWithDebInfo>:ProgramDatabase>>")
endif()
project ("Magma")

# Set source files for the executable.
add_executable(Magma "main.cpp")

set_target_properties(Magma PROPERTIES
  CXX_STANDARD 17
  CXX_EXTENSIONS OFF
  COMPILE_WARNING_AS_ERROR OFF
)

# Compiler-specific options
if(MSVC)
  target_compile_options(Magma PRIVATE /W4)
else()
  target_compile_options(Magma PRIVATE -Wall -Wextra -pedantic)
endif()

if(RENDERING_API STREQUAL VULKAN)
	#include vulkan in the project
	message(STATUS "Using module to find Vulkan")

	# Find Vulkan Path using CMake's Vulkan Module
	# It is mandatory since we need both the headers and SDK
	# Reference to the instalation guide if needed
	find_package(Vulkan REQUIRED)

	IF (NOT Vulkan_FOUND)
		message(FATAL_ERROR "Could not find Vulkan library!")
	ELSE()
		message(STATUS ${Vulkan_LIBRARY})
	ENDIF()
endif()

if(RENDERING_API STREQUAL OPENGL)
	#include vulkan in the project
	message(STATUS "Using module to find OPENGL")

	# Find Vulkan Path using CMake's Vulkan Module
	# It is mandatory since we need both the headers and SDK
	# Reference to the instalation guide if needed
	find_package(OpenGL REQUIRED)

	IF (NOT OpenGL_FOUND)
		message(FATAL_ERROR "Could not find OpenGL library!")
	ELSE()
		message(STATUS ${OpenGL_LIBRARY})
	ENDIF()

	FetchContent_Declare(
    glad
    GIT_REPOSITORY https://github.com/Dav1dde/glad.git
    GIT_SHALLOW    TRUE
    GIT_PROGRESS   TRUE
	)

	FetchContent_GetProperties(glad)
	FetchContent_GetProperties(glad)
	if(NOT glad_POPULATED)
		message("Fetching glad")
		FetchContent_Populate(glad)
		set(GLAD_PROFILE "core" CACHE STRING "OpenGL profile")
		set(GLAD_API "gl=4.6" CACHE STRING "API type/version pairs, like \"gl=4.6\", no version means latest")
		set(GLAD_GENERATOR "c" CACHE STRING "Language to generate the binding for")
		set(GLAD_EXTENSIONS "GL_ARB_bindless_texture" CACHE STRING "Extensions to take into consideration when generating the bindings")
		add_subdirectory(${glad_SOURCE_DIR} ${glad_BINARY_DIR})
	endif()
endif()


FetchContent_Declare(
  glfw
  GIT_REPOSITORY https://github.com/glfw/glfw
  GIT_TAG master
)
FetchContent_MakeAvailable(glfw)

FetchContent_Declare(
  glm
  GIT_REPOSITORY https://github.com/g-truc/glm
  GIT_TAG master
)
FetchContent_MakeAvailable(glm)

# Include Vulkan as a targeted lib
if(RENDERING_API STREQUAL VULKAN)
	target_link_libraries(${PROJECT_NAME} PUBLIC Vulkan::Vulkan)
endif()


target_link_libraries(${PROJECT_NAME} PUBLIC glfw glm)


