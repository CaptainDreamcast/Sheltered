cmake_minimum_required(VERSION 3.25) 
project(MarchGame CXX)               
set(CMAKE_CXX_STANDARD 17)            
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

include("${CMAKE_SOURCE_DIR}/../../addons/prism/cmake/prism.cmake" REQUIRED)

# Add source/header files to project
file(GLOB_RECURSE SOURCES ../*.cpp SOURCES ../*.h SOURCES ../*.rc)
list(FILTER SOURCES EXCLUDE REGEX ".*web.*")
list(FILTER SOURCES EXCLUDE REGEX ".*/build/.*")

add_link_options(/NODEFAULTLIB:libcmt.lib)
add_link_options(/IGNORE:4099,4286,4098)
add_link_options(/OPT:REF)
add_link_options(/OPT:ICF)

# Define exe
add_executable(MarchGame ${SOURCES})

# Link with libraries
target_link_libraries(MarchGame prism)
target_link_libraries(MarchGame SDL2)
target_link_libraries(MarchGame SDL2main)
target_link_libraries(MarchGame SDL2_image)
target_link_libraries(MarchGame fmod)
target_link_libraries(MarchGame SDL2_ttf)
target_link_libraries(MarchGame enet)
target_link_libraries(MarchGame freetype)
target_link_libraries(MarchGame glew)
target_link_libraries(MarchGame curl)
target_link_libraries(MarchGame libjpeg)
target_link_libraries(MarchGame libpng)
target_link_libraries(MarchGame libwebp)
target_link_libraries(MarchGame zstd)
target_link_libraries(MarchGame zlib)
target_link_libraries(MarchGame crypt32)
target_link_libraries(MarchGame wldap32)
target_link_libraries(MarchGame ws2_32)
target_link_libraries(MarchGame winmm)
target_link_libraries(MarchGame OpenGL32)
target_link_libraries(MarchGame version)

# Define preprocessor definitions
target_compile_definitions(MarchGame PUBLIC UNICODE)
target_compile_definitions(MarchGame PUBLIC _UNICODE)

set_property(TARGET MarchGame PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
target_compile_options(MarchGame PRIVATE /Gy)

set_target_properties(MarchGame PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/../assets)
set_target_properties(MarchGame PROPERTIES RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_SOURCE_DIR}/../assets)

# Copy over DLLs
file(GLOB DLLS "${CMAKE_SOURCE_DIR}/../../addons/prism/windows/vs17/DLL/*.dll")
foreach(DLL ${DLLS})
  add_custom_command(TARGET MarchGame POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    ${DLL}
    $<TARGET_FILE_DIR:MarchGame>)
endforeach()

# Optimize for release build
if(CMAKE_BUILD_TYPE STREQUAL "Release")
  target_compile_options(MarchGame PRIVATE /O2)
endif()
