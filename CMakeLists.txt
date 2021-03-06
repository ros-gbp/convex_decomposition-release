cmake_minimum_required(VERSION 2.8)
project(convex_decomposition)

find_package(catkin REQUIRED)

catkin_stack()
catkin_project(${PROJECT_NAME})

include(ExternalProject)

set(CMAKE_BUILD_TYPE Release)

# download and build ConvexDecomposition
ExternalProject_Add(cd
  DOWNLOAD_DIR "${CMAKE_SOURCE_DIR}/download"
  URL "http://pr.willowgarage.com/downloads/ConvexDecomposition.zip"
  URL_MD5 "bc4a79ca0d6b282d472d118c95f83867"
  CMAKE_ARGS
  "-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}"
  "-DCMAKE_INSTALL_PREFIX=${CMAKE_SOURCE_DIR}/cd-inst"
  PATCH_COMMAND patch -p0 < ${CMAKE_SOURCE_DIR}/convex_decomposition.patch)

# use a CMakeLists.txt file to configure build of PQP
ExternalProject_Add_Step(cd addCMakeList
  COMMAND ${CMAKE_COMMAND} -E copy
  "${CMAKE_SOURCE_DIR}/CMakeLists-CD.txt"
  "${CMAKE_BINARY_DIR}/cd-prefix/src/cd/CMakeLists.txt"
  DEPENDEES download
  DEPENDERS configure)


install(PROGRAMS ${CMAKE_SOURCE_DIR}/cd-inst/bin/${PROJECT_NAME}		
  DESTINATION bin)
