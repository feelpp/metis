# libmetis/CMakeLists.txt

# collect all .c sources in this directory
file(GLOB METIS_SOURCES
  "${CMAKE_CURRENT_SOURCE_DIR}/*.c"
)

add_library(metis
  ${METIS_LIBRARY_TYPE}
  ${METIS_SOURCES}
)

target_include_directories(metis
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/libmetis>
    $<BUILD_INTERFACE:${GKLIB_PATH}/include>
    $<INSTALL_INTERFACE:include/metis>
    
)

target_compile_definitions(metis
  PRIVATE
    IDXTYPEWIDTH=32
    REALTYPEWIDTH=32
)

target_link_libraries(metis
  PUBLIC m GKlib::GKlib

)

add_library(Metis::metis ALIAS metis)

# install headers
install(
  DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/
  DESTINATION include/metis
  FILES_MATCHING PATTERN "*.h"
)

install(
  TARGETS metis
    EXPORT MetisTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)