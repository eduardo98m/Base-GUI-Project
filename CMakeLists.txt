cmake_minimum_required(VERSION 3.10)
SET(PROJECT_NAME "BaseGUI")

project(${PROJECT_NAME})
# specify the C++ standard
set(CMAKE_CXX_STANDARD 14)

# Add your source files
set(SOURCES 
    "src/main.cpp")

# Add ImGui source files
set(IMGUI_SOURCES 
    "thirdParty/imgui/imgui.cpp" 
    "thirdParty/imgui/imgui_draw.cpp" 
    "thirdParty/imgui/imgui_widgets.cpp" 
    "thirdParty/imgui/imgui_tables.cpp"
    "thirdParty/imgui/backends/imgui_impl_opengl3.cpp" 
    "thirdParty/imgui/backends/imgui_impl_glfw.cpp")


# Add ImPlot source files
set(IMPLOT_SOURCES 
    "thirdParty/implot/implot.cpp" 
    "thirdParty/implot/implot_items.cpp")

# If you're on Linux, use the OpenGL3 and GLFW backends
if(UNIX AND NOT APPLE)
    add_subdirectory("thirdParty/glfw")
    include_directories("thirdParty/glfw/include")
    set(OpenGL_GL_PREFERENCE GLVND)
    find_package(OpenGL REQUIRED)
    add_definitions(-DIMGUI_IMPL_OPENGL_LOADER_GLAD)
    list(APPEND IMGUI_SOURCES "thirdParty/imgui/backends/imgui_impl_opengl3.cpp" "thirdParty/imgui/backends/imgui_impl_glfw.cpp")

    set(OPENGL_opengl_LIBRARY "/usr/lib/x86_64-linux-gnu/libGL.so")
    set(OPENGL_glx_LIBRARY "/usr/lib/x86_64-linux-gnu/libGLX.so")
    set(OPENGL_INCLUDE_DIR "/usr/include/GL")
endif()

# If you're on Mac, use the OpenGL2 (legacy) and GLFW backends
if(APPLE)
    find_package(OpenGL REQUIRED)
    find_package(glfw3 REQUIRED)
    list(APPEND IMGUI_SOURCES "thirdParty/imgui/backends/imgui_impl_opengl2.cpp" "thirdParty/imgui/backends/imgui_impl_glfw.cpp")
endif()

# If you're on Windows, use the DirectX11 and Win32 backends
if(WIN32)
    list(APPEND IMGUI_SOURCES "thirdParty/imgui/backends/imgui_impl_dx11.cpp" "thirdParty/imgui/backends/imgui_impl_win32.cpp")
endif()

add_executable(${PROJECT_NAME} ${SOURCES} ${IMGUI_SOURCES} ${IMPLOT_SOURCES})

target_include_directories(${PROJECT_NAME} PRIVATE "thirdParty/imgui" "thirdParty/implot")

if(UNIX AND NOT APPLE)
    target_link_libraries(${PROJECT_NAME} glfw ${OPENGL_LIBRARIES})
endif()

if(APPLE)
    target_link_libraries(${PROJECT_NAME} glfw ${OPENGL_LIBRARIES})
endif()

if(WIN32)
    target_link_libraries(${PROJECT_NAME} d3d11.lib)
endif()
