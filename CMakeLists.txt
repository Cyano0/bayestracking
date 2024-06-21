cmake_minimum_required(VERSION 3.5)
project(bayes_tracking)

set(CPACK_PACKAGE_NAME "bayes_tracking")
set(VERSION "1.0.3")

find_package(ament_cmake REQUIRED)
find_package(OpenCV REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)

include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(include)

## Source files
add_library(${PROJECT_NAME} STATIC 
    src/bayes_tracking/associationmatrix.cpp 
    src/bayes_tracking/ekfilter.cpp 
    src/bayes_tracking/ukfilter.cpp 
    src/bayes_tracking/pfilter.cpp 
    src/bayes_tracking/models.cpp
    src/bayes_tracking/BayesFilter/bayesFltAlg.cpp
    src/bayes_tracking/BayesFilter/bayesFlt.cpp
    src/bayes_tracking/BayesFilter/CIFlt.cpp
    src/bayes_tracking/BayesFilter/covFlt.cpp
    src/bayes_tracking/BayesFilter/infFlt.cpp
    src/bayes_tracking/BayesFilter/infRtFlt.cpp
    src/bayes_tracking/BayesFilter/itrFlt.cpp
    src/bayes_tracking/BayesFilter/matSup.cpp
    src/bayes_tracking/BayesFilter/SIRFlt.cpp
    src/bayes_tracking/BayesFilter/UDFlt.cpp
    src/bayes_tracking/BayesFilter/UdU.cpp
    src/bayes_tracking/BayesFilter/unsFlt.cpp
)

target_link_libraries(${PROJECT_NAME}
  ${OpenCV_LIBS}
)

install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME}
  PATTERN ".svn" EXCLUDE
)

## Optional builds: examples
option(BAYESTRACKING_BUILD_EXAMPLES "Generates example programs" OFF)
if(BAYESTRACKING_BUILD_EXAMPLES)
  add_subdirectory(examples)
endif()

## Optional builds: documentation
option(BAYESTRACKING_BUILD_DOC "Generates API documentation" OFF)
if(BAYESTRACKING_BUILD_DOC)
  find_package(Doxygen)
  if (DOXYGEN_FOUND)
    configure_file(${CMAKE_SOURCE_DIR}/Doxyfile.in ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)

    add_custom_target(doc ALL
      ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile
      COMMENT "Generating API documentation with Doxygen" VERBATIM
    )

    set_property(DIRECTORY APPEND PROPERTY ADDITIONAL_MAKE_CLEAN_FILES doc)

    set(DOC_PATH "share/doc/${CPACK_PACKAGE_NAME}-${VERSION}")
    install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/doc/html
      DESTINATION ${DOC_PATH}
    )
  else()
    message(FATAL_ERROR "Doxygen not found. Please get a copy http://www.doxygen.org")
  endif()
endif()

ament_export_dependencies(OpenCV rclcpp std_msgs sensor_msgs)
ament_export_include_directories(include)
ament_export_libraries(${PROJECT_NAME})

ament_package()