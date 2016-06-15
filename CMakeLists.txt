cmake_minimum_required(VERSION 2.8)
project(ecto_pcl)

find_package(Qhull REQUIRED)
SET( QHULL_LIBRARY "${Qhull_LIBRARIES}" )

# Deal with catkin
find_package(catkin REQUIRED)
# Handle optional ROS dependencies
find_package(sensor_msgs QUIET)
find_package(pcl_conversions QUIET)
if(sensor_msgs_FOUND AND pcl_conversions_FOUND)
    find_package(catkin REQUIRED ecto sensor_msgs pcl_conversions)
    catkin_package(INCLUDE_DIRS include
            CATKIN_DEPENDS pcl_conversions sensor_msgs
            )
    set(FOUND_ROS_DEPS TRUE)
else()
    find_package(catkin REQUIRED ecto)
    catkin_package(INCLUDE_DIRS include
            CATKIN_DEPENDS
            )
    set(FOUND_ROS_DEPS FALSE)
endif()




# continue with the find_package
find_package(PCL REQUIRED)
# Remove vtkproj4 from PCL_LIBRARIES as bug
# https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=819741
list(REMOVE_ITEM PCL_LIBRARIES vtkproj4)
include_directories(SYSTEM
                    ${PCL_INCLUDE_DIRS}
)
include_directories(include ${catkin_INCLUDE_DIRS})

find_package(Boost COMPONENTS signals thread)

ecto_sphinx(${CMAKE_CURRENT_SOURCE_DIR}/doc doc)

# install the include directory
install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ecto_pcl/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
        COMPONENT main
)


add_subdirectory(src)

enable_testing()
add_subdirectory(test)
add_subdirectory(samples)
