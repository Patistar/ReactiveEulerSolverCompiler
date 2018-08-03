cmake_minimum_required(VERSION 3.9)

project(ReactiveEulerSolverCompiler)

include(ExternalProject)

## Configuration to enable all projects to use the same compiler and flags
set(CMAKE_C_COMPILER_ID "GNU")
set(CMAKE_CXX_COMPILER_ID "GNU")
# C flags
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -std=c99")
set(CMAKE_C_FLAGS_DEBUG "-O0 -g3 -ggdb")
set(CMAKE_C_FLAGS_RELEASE "-O2")

message("COMPILER: " ${CMAKE_C_COMPILER})

# C++ flags
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=c++14")
set(CMAKE_CXX_FLAGS_DEBUG "-O0 -g3 -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "-O2")

# Forward settings for external projects.
set(CMAKE_EXTERNALPROJECT_FORWARDS
	-DCMAKE_C_COMPILER=${CMAKE_C_COMPILER}
	-DCMAKE_C_COMPILER_ARG1=${CMAKE_C_COMPILER_ARG1}
	-DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}
	-DCMAKE_CXX_COMPILER_ARG1=${CMAKE_CXX_COMPILER_ARG1}
	-DCMAKE_MAKE_PROGRAM=${CMAKE_MAKE_PROGRAM}
	-DBUILD_SHARED_LIBS=${BUILD_SHARED_LIBS}
#	-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
)


## External Projects Sections
ExternalProject_Add(
    hpx
    GIT_REPOSITORY  https://github.com/STEllAR-GROUP/hpx
    GIT_SHALLOW     ON
    GIT_PROGRESS    ON
    CMAKE_ARGS      ${CMAKE_EXTERNALPROJECT_FORWARDS}
)