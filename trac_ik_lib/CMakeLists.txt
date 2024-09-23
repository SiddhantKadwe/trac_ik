cmake_minimum_required(VERSION 2.8.3)
find_package(Rock)
rock_init(trac_ik 1.4.11)


project(trac_ik)
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}.pc.in ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/pkgconfig)


set(EXCLUDE_DIR "/opt/ros/")
set(CMAKE_IGNORE_PATH "/opt/ros/")

find_package(Boost REQUIRED COMPONENTS system thread regex filesystem program_options date_time math_c99 math_c99f math_c99l math_tr1 math_tr1f math_tr1l)
find_package(PkgConfig REQUIRED)
pkg_check_modules(SDFormat QUIET sdformat)
pkg_check_modules(SDFormat9 QUIET sdformat9)
pkg_check_modules(SDFormat12 QUIET sdformat12)
pkg_check_modules(pkg_nlopt REQUIRED nlopt_cxx)
pkg_check_modules(Eigen REQUIRED eigen3)

if(SDFormat_FOUND)
  message(STATUS "Found SDFormat")
  set(SDFORMAT_PKGDEPEND sdformat)
  set(SDFORMAT_INCLUDE_DIRS ${SDFormat_INCLUDE_DIRS})
elseif(SDFormat9_FOUND)
  message(STATUS "Found SDFormat9")
  set(SDFORMAT_PKGDEPEND sdformat9)
  set(SDFORMAT_INCLUDE_DIRS ${SDFormat9_INCLUDE_DIRS})
elseif(SDFormat12_FOUND)
  message(STATUS "Found SDFormat12")
  set(SDFORMAT_PKGDEPEND sdformat12)
  set(SDFORMAT_INCLUDE_DIRS ${SDFormat12_INCLUDE_DIRS})
else()
  message(FATAL_ERROR "Could not find sdformat, sdformat9 or sdformat12 using pkg-config")
endif()

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
  ${SDFORMAT_INCLUDE_DIRS}
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
    DEPS_PKGCONFIG base-types base-lib eigen3 nlopt_cxx kdl_parser ${SDFORMAT_PKGDEPEND} orocos-kdl urdfdom urdfdom_headers)

install(FILES ${HPP_FILES}
  DESTINATION ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME}
)
#MESSAGE(STATUS "INSTALL_DIR: " ${CMAKE_INSTALL_PREFIX} )

#install(TARGETS ${PROJECT_NAME}
  #LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)

