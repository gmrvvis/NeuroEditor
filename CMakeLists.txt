cmake_minimum_required (VERSION 3.2)
project (ReTracer VERSION 0.0.1)
set( ReTracer_VERSION_ABI 1 )

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
#  set(CMAKE_BUILD_TYPE Debug)
endif(NOT CMAKE_BUILD_TYPE)

# Disable in source building
if( "${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}" )
 message( FATAL "no in source building allowed." )
endif( )

list( APPEND CMAKE_MODULE_PATH
 ${PROJECT_SOURCE_DIR}/CMake
 ${CMAKE_SOURCE_DIR}/CMake/common )

if( NOT EXISTS ${CMAKE_SOURCE_DIR}/CMake/common/Common.cmake )
 message( FATAL_ERROR "CMake/common missing, run: git submodule update --init")
endif( )

include( GitExternal )

set( RETRACER_DESCRIPTION "ReTracer - Editor of neuro morphological tracings." )
set( RETRACER_LICENSE LGPL )
set( RETRACER_MAINTAINER "Juan Pedro Brito <juanpedro.brito@upm.es>" )
set( COMMON_PROJECT_DOMAIN es.gmrv )

include( Common )

#add_definitions(-DQT_NO_KEYWORDS)

common_find_package(Qt5Widgets SYSTEM REQUIRED)
common_find_package(Qt5Core SYSTEM REQUIRED)
common_find_package(Qt5Gui SYSTEM REQUIRED)
common_find_package(OpenGL SYSTEM REQUIRED)
common_find_package(GLUT SYSTEM REQUIRED)
common_find_package(OpenMesh SYSTEM REQUIRED)
common_find_package(QGLViewer SYSTEM REQUIRED)

common_find_package(PythonInterp REQUIRED)
common_find_package(PythonLibs REQUIRED)
common_find_package(Boost REQUIRED COMPONENTS
  date_time filesystem iostreams program_options regex system unit_test_framework
  python${USE_BOOST_PYTHON_VERSION})

#common_find_package(PyGEmS REQUIRED)
common_find_package(nsol REQUIRED)
common_find_package(Eigen3 REQUIRED SYSTEM)

#list(APPEND COMMON_FIND_PACKAGE_DEFINES
#  RETRACER_USE_PYTHON${USE_BOOST_PYTHON_VERSION})

common_find_package_post( )

# Needed because this var is set by PythonLibs and does
# not follow conventional naming
include_directories (# ${Boost_INCLUDE_DIRS}
  ${PYTHON_INCLUDE_DIRS})

if ( nsol_FOUND )
  set(RETRACER_DEPENDENT_LIBRARIES nsol ${Boost_LIBRARIES} ${PYTHON_LIBRARIES})
endif( )

add_subdirectory( ReTracer )

set(DOCS README.md LICENSE.txt)
install(FILES ${DOCS} DESTINATION share/ReTracer COMPONENT dev)

set(DOXYGEN_MAINPAGE_MD README.md)
set(DOXYGEN_EXTRA_INPUT ${PROJECT_SOURCE_DIR}/README.md)
include(DoxygenRule)

include(CPackConfig)
include(CTest)