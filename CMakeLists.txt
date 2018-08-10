cmake_minimum_required(VERSION 3.12)

############################################
########       Configuration        ########
############################################

## Configuration to enable all projects to use the same compiler and flags
set(CMAKE_C_COMPILER "gcc-8")
set(CMAKE_CXX_COMPILER "g++-8")
# C flags
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -std=c99")
set(CMAKE_C_FLAGS_DEBUG "-O0 -g3 -ggdb")
set(CMAKE_C_FLAGS_RELEASE "-O2")

message("COMPILER C ID: " ${CMAKE_C_COMPILER_ID})
message("COMPILER CXX ID: " ${CMAKE_C_COMPILER_ID})

message("COMPILER C: " ${CMAKE_C_COMPILER})
message("COMPILER CXX: " ${CMAKE_C_COMPILER})

# C++ flags
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=c++14")
set(CMAKE_CXX_FLAGS_DEBUG "-O0 -g3 -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "-O2")

# Install directory
message("Binary directory: " ${CMAKE_BINARY_DIR})
set(projects_install_dir ${CMAKE_BINARY_DIR}/projects/)

# Forward settings for external projects.
set(CMAKE_EXTERNALPROJECT_FORWARDS
	-DCMAKE_C_COMPILER_ID=${CMAKE_C_COMPILER_ID}
	-DCMAKE_C_COMPILER_ARG1=${CMAKE_C_COMPILER_ARG1}
	-DCMAKE_CXX_COMPILER_ID=${CMAKE_CXX_COMPILER_ID}
	-DCMAKE_CXX_COMPILER_ARG1=${CMAKE_CXX_COMPILER_ARG1}
	-DCMAKE_MAKE_PROGRAM=${CMAKE_MAKE_PROGRAM}
	-DBUILD_SHARED_LIBS=${BUILD_SHARED_LIBS}
#	-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
)

# Setup project

project(ReactiveEulerSolverCompiler)

include(ExternalProject)

###########################################
######## External Projects Section ########
###########################################

## Boost
ExternalProject_Add(
	Boost
	GIT_REPOSITORY 	https://github.com/boostorg/boost
	GIT_TAG			boost-1.68.0
	#GIT_SUBMODULES libs/core libs/config libs/io
	GIT_SHALLOW     ON
    GIT_PROGRESS    ON
	# TODO: Set BUILD TYPE and COMPILER for Boost
	BUILD_IN_SOURCE 1
	CONFIGURE_COMMAND pwd COMMAND ../Boost/bootstrap.sh --prefix=${projects_install_dir}/Boost
	BUILD_COMMAND ./b2 install -j8   --prefix=${projects_install_dir}/Boost
	INSTALL_COMMAND ""
)

## Install hwloc (required by hpx)
ExternalProject_Add(
	hwloc
	GIT_REPOSITORY 		https://github.com/open-mpi/hwloc
	GIT_SHALLOW     	ON
    GIT_PROGRESS    	ON
	# TODO: Set BUILD TYPE and COMPILER for hwloc
	CONFIGURE_COMMAND 	../hwloc/autogen.sh COMMAND ../hwloc/configure --prefix=${projects_install_dir}/hwloc
	BUILD_COMMAND 		make
	INSTALL_COMMAND 	make install
)

## Install hpx
ExternalProject_Add(
	hpx
	DEPENDS				hwloc Boost
	GIT_REPOSITORY  	https://github.com/STEllAR-GROUP/hpx
	GIT_TAG				1.1.0
	GIT_SHALLOW     	ON
	GIT_PROGRESS    	ON
	CMAKE_ARGS      	${CMAKE_EXTERNALPROJECT_FORWARDS}
						-DHPX_WITH_EXAMPLES=OFF
						-DHPX_WITH_TESTS=OFF
						-DHWLOC_ROOT=${projects_install_dir}/hwloc
						-DBOOST_ROOT=${projects_install_dir}/Boost
						-DBoost_NO_SYSTEM_PATHS=ON
						-DCMAKE_INSTALL_PREFIX=${projects_install_dir}/hpx
)

## TODO: cgns, google benchmark