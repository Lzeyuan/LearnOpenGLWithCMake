cmake_minimum_required (VERSION 3.25)

if(VCPKG_TOOLCHAIN_FILE)
    string(REPLACE "\\" "/" converted_path "${VCPKG_TOOLCHAIN_FILE}")
	list(APPEND CMAKE_TOOLCHAIN_FILE ${converted_path})
else()
    message(FATAL_ERROR "please set vcpkg tool file")
endif()

project(03_CMakePresetsAndVcpkg)

add_executable(
    ${PROJECT_NAME}
    main.cpp
)

add_subdirectory(3rd)

find_package(glfw3 CONFIG REQUIRED)

target_link_libraries(
    ${PROJECT_NAME}
    PRIVATE
    glad
    glfw
)

install(
    TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION bin
)