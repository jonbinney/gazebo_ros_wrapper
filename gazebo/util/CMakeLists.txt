include (${gazebo_cmake_dir}/GazeboUtils.cmake)

if (HAVE_OPENAL)
  include_directories(${OPENAL_INCLUDE_DIR})
endif()

set (sources 
  Diagnostics.cc
  LogPlay.cc
  LogRecord.cc
  OpenAL.cc
)

set (headers 
  Diagnostics.hh
  LogPlay.hh
  LogRecord.hh
  OpenAL.hh
  UtilTypes.hh
)

set (gtest_sources
  Diagnostics_TEST.cc
  LogRecord_TEST.cc
  OpenAL_TEST.cc
)

gz_build_tests(${gtest_sources})

set (util_headers "" CACHE INTERNAL "util headers" FORCE)
foreach (hdr ${headers})
  APPEND_TO_CACHED_STRING(util_headers
    "Utility Headers" "#include \"gazebo/util/${hdr}\"\n")
endforeach()

configure_file (${CMAKE_CURRENT_SOURCE_DIR}/util.hh.in
  ${CMAKE_CURRENT_BINARY_DIR}/util.hh )

gz_add_library(gazebo_util ${sources})

add_dependencies(gazebo_util
  gazebo_common
  gazebo_msgs
  gazebo_math)

target_link_libraries(gazebo_util
  gazebo_common
  gazebo_transport
  gazebo_math
  gazebo_msgs
)

gz_install_library(gazebo_util)
gz_install_includes("util" ${headers} ${CMAKE_CURRENT_BINARY_DIR}/util.hh)
