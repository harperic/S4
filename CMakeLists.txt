CMAKE_MINIMUM_REQUIRED(VERSION 2.6.2 FATAL_ERROR)

if (CMAKE_MAJOR_VERSION VERSION_GREATER 2)
if(COMMAND cmake_policy)
    cmake_policy(SET CMP0003 NEW)
    cmake_policy(SET CMP0026 OLD)
    cmake_policy(SET CMP0042 OLD)
endif(COMMAND cmake_policy)
endif()

project (S4)

add_subdirectory(CMake)

# find TBB lib and includes
# find_library(TBB_LIBRARY tbb
             # PATHS ENV TBB_LINK)
# find_path(TBB_INCLUDE_DIR tbb/tbb.h
          # PATHS ENV TBB_INC)
# include_directories(${TBB_INCLUDE_DIR})
# if (TBB_LIBRARY)
    # mark_as_advanced(TBB_LIBRARY)
# endif()
# if (TBB_INCLUDE_DIR)
    # mark_as_advanced(TBB_INCLUDE_DIR)
# endif()

# find ISPC
# find_program(ISPC_EXECUTABLE ispc)
# if (ISPC_EXECUTABLE)
#     mark_as_advanced(ISPC_EXECUTABLE)
# endif()

include(FindLua)
include_directories(${LUA_INCLUDES})
include(FindFFTW)
include_directories(${FFTW3_INC})
include(FindAccelerators)
include_directories(${vecLib_INCLUDES})

# set(BLAS_LIBRARIES "-framework Accelerate")
# mark_as_advanced(BLAS_LIBRARIES)
# set(LAPACK_LIBRARIES -framework Accelerate)
# mark_as_advanced(LAPACK_LIBRARIES LAPACK_INCLUDES)

include(S4CFlagsSetup)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -Wall -msse3 -msse2 -msse -Wno-unknown-pragmas -Wno-deprecated-declarations")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -Wall -msse3 -msse2 -msse")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC -Wall -msse3 -msse2 -msse")

# setup python
include(S4PythonSetup)
include_directories(${PYTHON_INCLUDE_DIR})

# setup cython
# include(S4CythonSetup)
find_package(Cython)
include(UseCython)

# setup numpy
include (S4NumpySetup)
include_directories(${NUMPY_INCLUDE_DIR})
# cython uses the deprecated api, so these remain commented til further notice
# add_definitions(-DPY_ARRAY_UNIQUE_SYMBOL=PyArrayHandle)
# add_definitions(-DNPY_NO_DEPRECATED_API=NPY_1_7_API_VERSION)

# macros
include (S4Macros)
# mpi
include(S4MPISetup)
include_directories(${MPI_CXX_INCLUDE_DIRS})
link_directories(${MPI_CXX_LIBRARIES})

# include external source projects
# include_directories(${CMAKE_CURRENT_SOURCE_DIR}/extern)

# set (UPDATE_SUBMODULES 1 CACHE BOOL "Update git submodules for dependency projects")

# if (UPDATE_SUBMODULES)
  # message(STATUS "Updating git submodules")
  # execute_process(COMMAND git submodule update --init
                  # WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
# else (UPDATE_SUBMODULES)
  # message(STATUS "Not updating git submodules")
# endif (UPDATE_SUBMODULES)

# set the default install prefix
IF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    SET(CMAKE_INSTALL_PREFIX ${PYTHON_USER_SITE} CACHE PATH "Python site installation directory (defaults to USER_SITE)" FORCE)
ENDIF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)

# Fixup conda linking, if this python appears to be a conda python
# if (${CMAKE_MAJOR_VERSION} GREATER 2)
  # get_filename_component(_python_bin_dir ${PYTHON_EXECUTABLE} DIRECTORY)
  # if (EXISTS "${_python_bin_dir}/conda")
      # message("-- Detected conda python, activating workaround")
      # set(_using_conda On)
  # else()
      # set(_using_conda Off)
  # endif()
# endif()

#add_subdirectory(freud)
# include(freud/CMakeLists.txt)
add_subdirectory(S4)
# add_subdirectory(cpp)
# add_subdirectory(doc)