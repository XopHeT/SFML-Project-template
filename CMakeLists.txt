cmake_minimum_required(VERSION 3.19)
set(CMAKE_CXX_STANDARD 17)

# Enable debug symbols by default
# must be done before project() statement
if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build (Debug or Release)" FORCE)
endif ()
# (you can also set it on the command line: -D CMAKE_BUILD_TYPE=Release)

project(SFML_ProjectTemplate)

# Set version information in a config.h file
set(VERSION_MAJOR 1)
set(VERSION_MINOR 0)
configure_file(
        "${PROJECT_SOURCE_DIR}/config.h.in"
        "${PROJECT_BINARY_DIR}/config.h"
)
include_directories("${PROJECT_BINARY_DIR}")
include_directories(SFML/include)
add_subdirectory(SFML)

set(SFML_STATIC_LIBRARIES FALSE)
set(SFML_DIR "${PROJECT_BINARY_DIR}/SFML")
set(EXECUTABLE_NAME ${CMAKE_PROJECT_NAME})
add_executable(${EXECUTABLE_NAME} WIN32 main.cpp)

# Detect and add SFML
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake_modules" ${CMAKE_MODULE_PATH})
#Find any version 2.X of SFML
#See the FindSFML.cmake file for additional details and instructions
find_package(SFML 2.5 REQUIRED network audio graphics window system)
if (SFML_FOUND)
    include_directories(${SFML_INCLUDE_DIR})
    target_link_libraries(${EXECUTABLE_NAME} sfml-network sfml-audio sfml-graphics sfml-window sfml-system)
endif ()

# Install target
install(TARGETS ${EXECUTABLE_NAME} DESTINATION bin)
if (MINGW)
    message(STATUS "    Installing system-libraries: MinGW DLLs.")
    get_filename_component(Mingw_Path ${CMAKE_CXX_COMPILER} PATH)
    set(CMAKE_INSTALL_SYSTEM_RUNTIME_LIBS ${Mingw_Path}/libstdc++-6.dll ${Mingw_Path}/libgcc_s_seh-1.dll ${Mingw_Path}/libwinpthread-1.dll)
endif (MINGW)

# CPack packaging
include(InstallRequiredSystemLibraries)
set(CPACK_PACKAGE_VERSION_MAJOR "${VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${VERSION_MINOR}")
include(CPack)
