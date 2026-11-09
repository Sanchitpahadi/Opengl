
cmake_minimum_required(VERSION 3.16)

project(Game LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find OpenGL
find_package(OpenGL REQUIRED)

# Find GLFW headers and library in your existing dev folder
find_path(GLFW_INCLUDE_DIR
    GLFW/glfw3.h
    PATHS "${CMAKE_SOURCE_DIR}/dev"
    NO_DEFAULT_PATH
)

find_library(GLFW_LIBRARY
    NAMES glfw3 glfw
    PATHS
        "${CMAKE_SOURCE_DIR}/dev/lib"
        "${CMAKE_SOURCE_DIR}/dev/lib-vc2026"
    NO_DEFAULT_PATH
)

if(NOT GLFW_INCLUDE_DIR OR NOT GLFW_LIBRARY)
    message(FATAL_ERROR "GLFW headers or library not found. Check your dev folder.")
endif()

# Create a GLFW target
add_library(glfw UNKNOWN IMPORTED)

set_target_properties(glfw PROPERTIES
    IMPORTED_LOCATION "${GLFW_LIBRARY}"
    INTERFACE_INCLUDE_DIRECTORIES "${GLFW_INCLUDE_DIR}"
)

# Your executable
add_executable(Game
    src/glad.c
    src/main.cpp
    src/Texture.cpp
    src/VertexArray.cpp
    src/VertexBuffer.cpp
    src/VertexBufferLayout.cpp
    src/TextRenderer.cpp
    src/Window.cpp
    src/Renderer.cpp
    src/Mesh.cpp
)

# Include directories
target_include_directories(Game PRIVATE
    "${CMAKE_SOURCE_DIR}/dev/include"
    "${CMAKE_SOURCE_DIR}/src/include"
)

# Link dependencies
target_link_libraries(Game PRIVATE
    OpenGL::GL
    glfw
)

# Copy shader and texture folders beside the executable
add_custom_command(TARGET Game POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    "${CMAKE_SOURCE_DIR}/src/shader"
    "$<TARGET_FILE_DIR:Game>/shader"

    COMMAND ${CMAKE_COMMAND} -E copy_directory
    "${CMAKE_SOURCE_DIR}/src/texture"
    "$<TARGET_FILE_DIR:Game>/texture"
)

# Set Visual Studio's working directory
set_property(TARGET Game PROPERTY
    VS_DEBUGGER_WORKING_DIRECTORY "$<TARGET_FILE_DIR:Game>"
)
