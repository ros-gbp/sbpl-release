cmake_minimum_required(VERSION 2.4.6)
PROJECT( sbpl )

set(SBPL_MAJOR_VERSION 1)
set(SBPL_MINOR_VERSION 0)
set(SBPL_PATCH_VERSION 0)

set(SBPL_VERSION ${SBPL_MAJOR_VERSION}.{$SBPL_MINOR_VERSION}.${SBPL_PATCH_VERSION})

SET (CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/CMakeModules")

# COMPILER SETTINGS (default: Release) and flags
INCLUDE(CompilerSettings)

if(WITH_ROS)
  # Add a ROS C++ macro to tell SBPL to use ROS print statements
  add_definitions(-DROS)  
endif(WITH_ROS)

SET( BASE_DIR ${CMAKE_SOURCE_DIR} )
SET( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${BASE_DIR}/lib )
SET( CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${BASE_DIR}/lib )
SET( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BASE_DIR}/bin )

set(INCLUDE_DIRS "${PROJECT_SOURCE_DIR}/include")
INCLUDE_DIRECTORIES(${INCLUDE_DIRS})

LINK_DIRECTORIES(${CMAKE_LIBRARY_OUTPUT_DIRECTORY})

# Installation

set(INSTALL_TARGETS_DEFAULT_ARGS
RUNTIME DESTINATION bin
LIBRARY DESTINATION lib
ARCHIVE DESTINATION lib
)

add_library(sbpl 
  src/discrete_space_information/nav2d/environment_nav2D.cpp
  src/discrete_space_information/navxythetalat/environment_navxythetalat.cpp
  src/discrete_space_information/navxythetalat/environment_navxythetamlevlat.cpp
  src/discrete_space_information/nav2d_uu/environment_nav2Duu.cpp
  src/discrete_space_information/template/environment_XXX.cpp
  src/discrete_space_information/robarm/environment_robarm.cpp
  src/planners/ARAStar/araplanner.cpp
  src/planners/ADStar/adplanner.cpp
  src/planners/VI/viplanner.cpp
  src/planners/PPCP/ppcpplanner.cpp
  src/planners/RStar/rstarplanner.cpp
  src/utils/heap.cpp
  src/utils/mdp.cpp
  src/utils/utils.cpp
  src/utils/2Dgridsearch.cpp	
  )

# uninstall target
configure_file(
    "${PROJECT_SOURCE_DIR}/CMakeModules/CMakeUninstall.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    IMMEDIATE @ONLY)

add_custom_target(uninstall
    COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)

file(GLOB sbpl_HDRS ${PROJECT_SOURCE_DIR}/include/sbpl/*.h)
install(FILES ${sbpl_HDRS} DESTINATION include/sbpl)

# Export the package for use from the build-tree
# (this registers the build-tree with a global CMake-registry)
export(PACKAGE sbpl)
 
# Create a sbpl-config.cmake file for the use from the build tree
set(SBPL_INCLUDE_DIRS "${INCLUDE_DIRS}")
set(SBPL_LIB_DIR "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}")
# not used right now (export depends?)
#set(SBPL_CMAKE_DIR "${PROJECT_BINARY_DIR}")
configure_file(sbpl-config.cmake.in
  "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/cmake/sbpl/sbpl-config.cmake" @ONLY)
configure_file(sbpl-config-version.cmake.in
  "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/cmake/sbpl/sbpl-config-version.cmake" @ONLY)

# Create a sbpl-config.cmake file for the use from the install tree
# and install it
set(SBPL_INCLUDE_DIRS "${CMAKE_INSTALL_PREFIX}/include")
set(SBPL_LIB_DIR "${CMAKE_INSTALL_PREFIX}/lib")
#set(SBPL_CMAKE_DIR "${INSTALL_DATA_DIR}/FooBar/CMake")
configure_file(sbpl-config.cmake.in
  "${PROJECT_BINARY_DIR}/InstallFiles/sbpl-config.cmake" @ONLY)
configure_file(sbpl-config-version.cmake.in
  "${PROJECT_BINARY_DIR}/InstallFiles/sbpl-config-version.cmake" @ONLY)
install(FILES
  "${PROJECT_BINARY_DIR}/InstallFiles/sbpl-config.cmake"
  "${PROJECT_BINARY_DIR}/InstallFiles/sbpl-config-version.cmake"
  DESTINATION lib/cmake/sbpl/)

# Write pkgconfig-file:
include(InstallPkgConfigFile)
install_pkg_config_file(sbpl
    CFLAGS
    LIBS -lsbpl
    REQUIRES
    VERSION ${SBPL_MAJOR_VERSION}.${SBPL_MINOR_VERSION}.${SBPL_PATCH_VERSION})


# Build executables in the bin directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

add_executable(test_sbpl src/test/main.cpp)
target_link_libraries(test_sbpl sbpl)

add_executable(test_adjacency_list src/test/test_adjacency_list.cpp)
target_link_libraries(test_adjacency_list sbpl)

#download_test_data(http://pr.willowgarage.com/data/sbpl/env_examples/nav2d/willow-25mm-inflated-env.cfg env_examples/nav2d/willow-25mm-inflated-env.cfg 1b4551eeba6df03b6e597e598bbaa4f3)
