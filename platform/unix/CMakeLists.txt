# Cross-compiler settings
set(CMAKE_C_COMPILER gcc)
set(CMAKE_CXX_COMPILER g++)
set(CMAKE_ASM_COMPILER gcc)
set(CMAKE_AR ar)
set(CMAKE_OBJCOPY objcopy)
set(CMAKE_OBJDUMP objdump)
set(CMAKE_SIZE size)

add_definitions(-D__weak=__attribute__\(\(weak\)\) -D__packed=__attribute__\(\(__packed__\)\))
add_definitions(-DLWM2M_LITTLE_ENDIAN -DLWM2M_EMBEDDED_MODE -DUNIX)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${COMMON_FLAGS} -std=c++11")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${COMMON_FLAGS} -std=gnu99")

set(PLATFORM_DIRECTORY platform/unix)
file(GLOB_RECURSE UNIX_SOURCES "${PLATFORM_DIRECTORY}/src/*.*" "${PLATFORM_DIRECTORY}/include/*.*")

file(GLOB_RECURSE MIDDLEWARES_SOURCES)

set(PLATFORM_SOURCES ${PLATFORM_SOURCES} ${MIDDLEWARES_SOURCES} ${UNIX_SOURCES})
message("${PLATFORM_SOURCES}")

set(SOURCES ${SOURCES} ${PLATFORM_SOURCES})

include_directories(${PLATFORM_DIRECTORY}/include)

find_library(RT rt)
find_library(PTHREAD pthread)
find_library(LIBC c)
find_library(LIBM m)

add_executable(${PROJECT_NAME} ${SOURCES})
target_link_libraries(${PROJECT_NAME} ${RT} ${PTHREAD} ${LIBC} ${LIBM})

add_custom_target(firmware DEPENDS ${PROJECT_NAME})