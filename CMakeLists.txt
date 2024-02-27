# CMakeList.txt : CMake project for Ectoplasme, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.8)
project(Magma VERSION 0.1.0 LANGUAGES CXX C)

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
  COMPILE_WARNING_AS_ERROR ON
)

# Compiler-specific options
if(MSVC)
  target_compile_options(Magma PRIVATE /W4)
else()
  target_compile_options(Magma PRIVATE -Wall -Wextra -pedantic)
endif()

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
target_link_libraries(${PROJECT_NAME} PUBLIC Vulkan::Vulkan glfw glm)


