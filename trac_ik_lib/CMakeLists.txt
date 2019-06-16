cmake_minimum_required(VERSION 2.8.3)
find_package(Rock)
rock_init(trac_ik 1.4.11)


project(trac_ik)
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}.pc.in ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/pkgconfig)

if(CMAKE_COMPILER_IS_GNUCXX OR ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang"))
    set(CMAKE_CXX_FLAGS "-std=c++11 ${CMAKE_CXX_FLAGS}")
endif()


set(EXCLUDE_DIR "/opt/ros/")
set(CMAKE_IGNORE_PATH "/opt/ros/")

find_package(Boost REQUIRED COMPONENTS system thread regex filesystem program_options date_time math_c99 math_c99f math_c99l math_tr1 math_tr1f math_tr1l)
find_package(PkgConfig REQUIRED)
find_package(SDFormat REQUIRED)
pkg_check_modules(pkg_nlopt REQUIRED nlopt)
pkg_check_modules(Eigen REQUIRED eigen3)

# TODO: resolve libraries to absolute paths

file(GLOB_RECURSE CPP_FILES ${PROJECT_SOURCE_DIR}/src/*.cpp)
MESSAGE( STATUS "CPP_FILES: " ${CPP_FILES} )


file(GLOB_RECURSE HPP_FILES ${PROJECT_SOURCE_DIR}/include/*.h*)
MESSAGE( STATUS "HPP_FILES: " ${HPP_FILES} )
include_directories(
   include
   "/usr/include/"
  ${CMAKE_INSTALL_PREFIX}/include
  ${Eigen_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${pkg_nlopt_INCLUDE_DIRS}
  ${SDFormat_INCLUDE_DIRS}
)

rock_library(trac_ik
    SOURCES ${CPP_FILES}
    #HEADERS ${HPP_FILES}
    LIBS    ${pkg_nlopt_LIBRARIES}
            ${Boost_LIBRARIES}
            ${Boost_SYSTEM_LIBRARY}
            ${Boost_MATH_LIBRARY}
            ${Boost_FILESYSTEM_LIBRARY}
	        ${Boost_PROGRAM_OPTIONS_LIBRARY}
            ${Boost_THREAD_LIBRARY}
            ${Boost_MULTIPRECISION_LIBRARY}
    DEPS_PKGCONFIG base-types base-lib eigen3 nlopt sdformat kdl_parser orocos-kdl urdfdom urdfdom_headers)

install(FILES ${HPP_FILES}
  DESTINATION ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME}
)
#MESSAGE(STATUS "INSTALL_DIR: " ${CMAKE_INSTALL_PREFIX} )

#install(TARGETS ${PROJECT_NAME}
  #LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)

