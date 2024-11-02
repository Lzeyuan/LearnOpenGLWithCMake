cmake_minimum_required (VERSION 3.25)

set(CMAKE_TOOLCHAIN_FILE ${VCPKG_TOOLCHAIN_FILE} CACHE STRING "Vcpkg toolchain file")
set(VCPKG_TARGET_TRIPLET x64-windows)

project(03_CMakePresetsAndVcpkg)

find_package(glfw3 CONFIG REQUIRED)
message("VCPKG_ROOT = " $ENV{VCPKG_ROOT})
add_executable(
    ${PROJECT_NAME}
    "src/main.cpp"
)

add_subdirectory(3rd)

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