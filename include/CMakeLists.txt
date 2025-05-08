configure_file(metis.h.in metis.h @ONLY)
if(METIS_INSTALL)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/metis.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/metis)
endif()
