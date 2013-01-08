cmake_minimum_required(VERSION 2.8)
project(ObjectiveGitFramework)

add_custom_target(
    libgit2.a 
    COMMAND scripts/update_libgit2.sh
    COMMAND cp ${ObjectiveGitFramework_SOURCE_DIR}/libgit2.a ${CMAKE_BINARY_DIR} 
    WORKING_DIRECTORY ${ObjectiveGitFramework_SOURCE_DIR}
    COMMENT "Updating libgit2..."
)

# Collect sourcefiles
file(GLOB_RECURSE SRC Classes/*.m)
file(GLOB_RECURSE SRC_H Classes/*.h)

set(XCODE_FRAMEWORKS /Applications/Xcode.app/Contents/Developer/Library/Frameworks/)

set(CMAKE_C_COMPILER   "clang")  #minimum required version clang 3.2
set(CMAKE_CXX_COMPILER "clang")  #minimum required version clang 3.2

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu99")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fconstant-string-class=NSConstantString")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fmessage-length=0")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fblocks")

if("${CMAKE_SYSTEM}" MATCHES "Linux")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -MMD -MP -fno-strict-aliasing")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fexceptions -fobjc-exceptions")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_NATIVE_OBJC_EXCEPTIONS -fPIC")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -DGSWARN -DGSDIAGNOSE -Wno-import")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fobjc-runtime=gnustep")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fconstant-string-class=NSConstantString")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -I. -I$ENV{GNUSTEP_INSTALL_DIR}/include")
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fobjc-arc")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fasm-blocks")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Xclang -fobjc-default-synthesize-properties")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -include ${ObjectiveGitFramework_SOURCE_DIR}/ObjectiveGitFramework_Prefix.pch")


# CpHeaders into build dir ---------------------------------------------------# 
set(HEADER_DIR ${ObjectiveGitFramework_BINARY_DIR}/Headers)

execute_process (
    COMMAND ${CMAKE_COMMAND} -E make_directory ${HEADER_DIR} 
    COMMAND ${CMAKE_COMMAND} -E make_directory ${HEADER_DIR}/ObjectiveGit 
)

execute_process(
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${ObjectiveGitFramework_SOURCE_DIR}/libgit2/include ${HEADER_DIR} 
)

foreach(header ${SRC_H})
    execute_process (
        COMMAND ${CMAKE_COMMAND} -E copy ${header} ${HEADER_DIR}/ObjectiveGit
    )
endforeach()


# Create library ObjectiveGit ------------------------------------------------#
add_library(ObjectiveGit STATIC ${SRC})

include_directories(
    Classes 
    Classes/Categories/ 
    libgit2/include 
    ${HEADER_DIR}
)

add_dependencies(ObjectiveGit libgit2.a)
