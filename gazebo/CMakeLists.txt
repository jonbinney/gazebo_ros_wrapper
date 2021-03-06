include_directories(SYSTEM
  ${OPENGL_INCLUDE_DIR}
  ${OGRE_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${PROTOBUF_INCLUDE_DIR}
  ${SDF_INCLUDE_DIRS}
)

link_directories(
  ${ogre_library_dirs}
  ${Boost_LIBRARY_DIRS}
  ${SDF_LIBRARY_DIRS}
  ${PROJECT_BINARY_DIR}/test
)

if (HAVE_BULLET)
  link_directories(${BULLET_LIBRARY_DIRS})
endif()

add_subdirectory(msgs)
add_subdirectory(math)
add_subdirectory(transport)
add_subdirectory(common)
add_subdirectory(rendering)
add_subdirectory(gui)
add_subdirectory(physics)
add_subdirectory(sensors)
add_subdirectory(util)

add_dependencies(gazebo_physics gazebo_msgs)
add_dependencies(gazebo_physics_ode gazebo_msgs)
add_dependencies(gazebo_gui gazebo_msgs)
add_dependencies(gazebo_rendering gazebo_msgs)
add_dependencies(gazebo_sensors gazebo_rendering)

if(HAVE_BULLET)
  add_dependencies(gazebo_physics_bullet gazebo_msgs)
endif()
if (HAVE_SIMBODY)
  add_dependencies(gazebo_physics_simbody gazebo_msgs)
endif()
if(HAVE_DART)
  add_dependencies(gazebo_physics_dart gazebo_msgs)
endif()


gz_add_executable(gzserver server_main.cc Server.cc Master.cc gazebo.cc)
gz_add_executable(gazebo gazebo_main.cc Server.cc Master.cc gazebo.cc)

target_link_libraries(gzserver
  gazebo_common
  gazebo_util
  gazebo_transport
  gazebo_physics
  gazebo_sensors
  gazebo_rendering
  gazebo_msgs
  pthread
)

target_link_libraries(gazebo
  gazebo_common
  gazebo_util
  gazebo_transport
  gazebo_physics
  gazebo_sensors
  gazebo_rendering
  gazebo_msgs
  gazebo_gui
  pthread
)

if ("${CMAKE_BUILD_TYPE}" STREQUAL "CHECK")
  target_link_libraries(gzserver gcov)
endif()

gz_install_executable(gzserver)
gz_install_executable(gazebo)

gz_add_library(libgazebo Server.cc Master.cc gazebo.cc)
set_target_properties(libgazebo PROPERTIES OUTPUT_NAME "gazebo")

target_link_libraries(libgazebo
  gazebo_common
  gazebo_util
  gazebo_transport
  gazebo_physics
  gazebo_sensors
  gazebo_rendering
  gazebo_msgs
  gazebo_math
  pthread
)

add_dependencies(libgazebo gazebo_msgs)
gz_install_library(libgazebo)

set(headers Server.hh Master.hh gazebo.hh gazebo_core.hh)
gz_install_includes("" ${headers})
