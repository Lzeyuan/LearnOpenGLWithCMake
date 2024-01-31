# glfw动态库子项目
project(glfw)

# 仅共享动态库，没有源文件
add_library(
    ${PROJECT_NAME}
    INTERFACE
)

# 这里把头文件共享给引用的项目
target_include_directories(
    ${PROJECT_NAME}
    INTERFACE
    include
)

target_link_directories(
    ${PROJECT_NAME}
    INTERFACE
    lib
)

target_link_libraries(
    ${PROJECT_NAME}
    INTERFACE
    glfw3dll
)

install(
    FILES  ${CMAKE_CURRENT_SOURCE_DIR}/lib/glfw3.dll
    DESTINATION bin
)