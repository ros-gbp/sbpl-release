cmake_minimum_required(VERSION 2.4.6)
PROJECT( sbpl )
find_package(catkin)

catkin_project(sbpl
  LIBRARIES sbpl
  INCLUDE_DIRS src
  )	

if(WITH_ROS)
  # Add a ROS C++ macro to tell SBPL to use ROS print statements
  add_definitions(-DROS)  
endif(WITH_ROS)

add_library(sbpl SHARED
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

install(DIRECTORY src/ DESTINATION include/sbpl
	FILES_MATCHING PATTERN "*.h")
install(TARGETS sbpl
	LIBRARY DESTINATION lib)
install(FILES manifest.xml
	DESTINATION share/sbpl)

add_executable(test_sbpl src/test/main.cpp)
target_link_libraries(test_sbpl sbpl)

add_executable(test_adjacency_list src/test/test_adjacency_list.cpp)
target_link_libraries(test_adjacency_list sbpl)
