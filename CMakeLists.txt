cmake_minimum_required(VERSION 2.8.3)
project(mono_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/mono-2.0)

set(MONO_VERSION 5.4.1.7)
ExternalProject_Add(mono_src
  GIT_REPOSITORY git@github.com:mono/mono.git
  GIT_TAG mono-${MONO_VERSION}
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND
    cd ../mono_src/
    && ./autogen.sh
    --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND
    cd ../mono_src/
    && $(MAKE)
  INSTALL_COMMAND
    cd ../mono_src/
    && $(MAKE) install
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} mono_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libmono-2.0${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/mono-2.0
  LIBRARIES ${CATKIN_DEVEL_PREFIX}/lib/libmono-2.0${CMAKE_SHARED_LIBRARY_SUFFIX}
)
