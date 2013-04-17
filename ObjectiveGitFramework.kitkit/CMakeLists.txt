cmake_minimum_required(VERSION 2.8)
project(ObjectiveGitFramework)

set(PROJECT_DIR ${ObjectiveGitFramework_SOURCE_DIR}/..)

set(BUILD_SHARED_LIBS ON)
set(BUILD_CLAR OFF) 
set(THREADSAFE OFF)
add_subdirectory(${PROJECT_DIR}/libgit2 ${CMAKE_CURRENT_BINARY_DIR}/libgit2)

# Collect sourcefiles
file(GLOB_RECURSE SRC ${PROJECT_DIR}/Classes/*.m)
file(GLOB_RECURSE SRC_H ${PROJECT_DIR}/Classes/*.h)

include(KitKit)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -include ${PROJECT_DIR}/ObjectiveGitFramework_Prefix.pch")


# CpHeaders into build dir ---------------------------------------------------# 
set(HEADER_DIR ${ObjectiveGitFramework_BINARY_DIR}/Headers)

execute_process (
    COMMAND ${CMAKE_COMMAND} -E make_directory ${HEADER_DIR} 
    COMMAND ${CMAKE_COMMAND} -E make_directory ${HEADER_DIR}/ObjectiveGit 
)

execute_process(
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_DIR}/libgit2/include ${HEADER_DIR} 
)

foreach(header ${SRC_H})
    execute_process (
        COMMAND ${CMAKE_COMMAND} -E copy ${header} ${HEADER_DIR}/ObjectiveGit
    )
endforeach()


# Create library ObjectiveGit ------------------------------------------------#
add_library(ObjectiveGit SHARED ${SRC})

include_directories(
    ${PROJECT_DIR}/Classes 
    ${PROJECT_DIR}/Classes/Categories/ 
    ${PROJECT_DIR}/libgit2/include 
    ${HEADER_DIR}
)

target_link_libraries(
    ObjectiveGit 
    git2
    ${CMAKE_EXE_LINKER_FLAGS}
)

add_dependencies(ObjectiveGit git2)
