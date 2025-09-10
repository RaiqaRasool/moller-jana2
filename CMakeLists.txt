cmake_minimum_required(VERSION 3.16)
project(moller LANGUAGES CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(EVIO_LIB_PATH /w/epsci-scshelf2103/rasool/jana2.4.3/evio/Linux-x86_64)

# Find JANA library
find_package(JANA REQUIRED)

# Specify source files
set(SOURCES
    moller.cc
    JEventSource_EVIO.cc
    JEventProcessor_MOLLER.cc
    JFactory_FADC250.cc
)

# Create executable
add_executable(moller ${SOURCES})

# Include directories
target_include_directories(moller
    PRIVATE
        "${EVIO_LIB_PATH}/include"
        ${CMAKE_CURRENT_SOURCE_DIR}
)

# Link libraries
target_link_libraries(moller
    PRIVATE
        ${JANA_LIBRARY}
)

# Collect all EVIO .so files and link them
file(GLOB EVIO_LIBS "${EVIO_LIB_PATH}/lib64/*.so")
if(EVIO_LIBS)
    target_link_libraries(moller PRIVATE ${EVIO_LIBS})
endif()

# Set runtime search path for shared libraries
set_target_properties(moller PROPERTIES
    BUILD_RPATH "${$EVIO_LIB_PATH}/lib64"
)

