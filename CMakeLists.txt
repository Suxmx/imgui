cmake_minimum_required(VERSION 3.20)
project(imgui)

set(IMGUI_DIR "./")
list(APPEND IMGUI_INCLUDE_DIR "${IMGUI_DIR}" "${IMGUI_DIR}/backends")

# vulkan
find_package(Vulkan REQUIRED)
list(APPEND LINK_LIBS "Vulkan::Vulkan")

list(APPEND
        IMGUI_SOURCE
        ${IMGUI_DIR}/imgui.cpp
        ${IMGUI_DIR}/backends/imgui_impl_glfw.cpp
        ${IMGUI_DIR}/backends/imgui_impl_vulkan.cpp
        ${IMGUI_DIR}/imgui.cpp
        ${IMGUI_DIR}/imgui_draw.cpp
        ${IMGUI_DIR}/imgui_demo.cpp
        ${IMGUI_DIR}/imgui_tables.cpp
        ${IMGUI_DIR}/imgui_widgets.cpp)
# glfw
find_package(glfw3 QUIET)
if(NOT glfw3_FOUND)
    # 如果系统中没有找到 GLFW，可以使用项目中的 GLFW
    # 假设 GLFW 已经在项目结构中被添加
    if(TARGET glfw)
        list(APPEND LINK_LIBS "glfw")
    else()
        message(FATAL_ERROR "GLFW 库未找到，请确保已正确配置 GLFW")
    endif()
else()
    list(APPEND LINK_LIBS "glfw")
endif()

add_library(imgui STATIC ${IMGUI_SOURCE})
# 设置包含目录
target_include_directories(imgui
        PUBLIC ${IMGUI_INCLUDE_DIR}
        PRIVATE ${Vulkan_INCLUDE_DIRS}
)

# 链接依赖库
target_link_libraries(imgui PUBLIC ${LINK_LIBS})

# 设置编译选项
target_compile_definitions(imgui
        PUBLIC
#        IMGUI_IMPL_API
)

# 导出包含目录，方便其他项目使用
set(IMGUI_INCLUDE_DIRS ${IMGUI_INCLUDE_DIR} CACHE INTERNAL "ImGui 包含目录")

# 创建一个接口库，方便项目引用
add_library(imgui_interface INTERFACE)
target_include_directories(imgui_interface INTERFACE ${IMGUI_INCLUDE_DIR})
target_link_libraries(imgui_interface INTERFACE imgui)