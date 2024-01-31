cmake_minimum_required (VERSION 3.25)

project(01_CreateWindow)

add_executable(
    ${PROJECT_NAME}
    main.cpp
)

add_subdirectory(3rd)

target_link_libraries(
    ${PROJECT_NAME}
    PRIVATE
    glad
    glfw
)