cmake_minimum_required(VERSION 2.8.3)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

project(trac_ik)
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}.pc.in ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.pc DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/pkgconfig)

set(EXCLUDE_DIR "/opt/ros/")
set(CMAKE_IGNORE_PATH "/opt/ros/")

find_package(Boost REQUIRED COMPONENTS system thread regex filesystem program_options date_time math_c99 math_c99f math_c99l math_tr1 math_tr1f math_tr1l)
find_package(sdformat15 REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_check_modules(nlopt REQUIRED nlopt)
pkg_check_modules(Eigen REQUIRED eigen3)
pkg_check_modules(BASE_TYPES REQUIRED base_types)
pkg_check_modules(BASE_LOGGING REQUIRED base_logging)
pkg_check_modules(KDL_PARSER REQUIRED kdl_parser)
pkg_check_modules(URDFDOM REQUIRED urdfdom)
pkg_check_modules(URDFDOM_HEADERS REQUIRED urdfdom_headers)


if(sdformat15_FOUND)
  message(STATUS "Found SDFormat15")
  set(SDFORMAT_PKGDEPEND sdformat15)
  set(SDFORMAT_INCLUDE_DIRS ${sdformat15_INCLUDE_DIRS})
else()
  message(FATAL_ERROR "Could not find SDFormat15 using find_package")
endif()

# TODO: resolve libraries to absolute paths

file(GLOB_RECURSE CPP_FILES ${PROJECT_SOURCE_DIR}/src/*.cpp)
MESSAGE( STATUS "CPP_FILES: " ${CPP_FILES} )


file(GLOB_RECURSE HPP_FILES ${PROJECT_SOURCE_DIR}/include/*.h*)
MESSAGE( STATUS "HPP_FILES: " ${HPP_FILES} )

add_library(trac_ik ${CPP_FILES})

target_link_libraries(trac_ik
    ${BASE_TYPES_LIBRARIES}
    ${BASE_LOGGING_LIBRARIES}
    ${KDL_PARSER_LIBRARIES}
    ${URDFDOM_LIBRARIES}
    ${URDFDOM_HEADERS_LIBRARIES}
    ${nlopt_LIBRARIES}
    ${Boost_LIBRARIES}
)

target_include_directories(trac_ik PUBLIC
    include
    ${EIGEN3_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${SDFORMAT_INCLUDE_DIRS}
    ${BASE_TYPES_INCLUDE_DIRS}
    ${BASE_LOGGING_INCLUDE_DIRS}
    ${KDL_PARSER_INCLUDE_DIRS}
    ${URDFDOM_INCLUDE_DIRS}
    ${URDFDOM_HEADERS_INCLUDE_DIRS}
)

install(FILES ${HPP_FILES}
  DESTINATION ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME}
)

install(TARGETS ${PROJECT_NAME}
  LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)

