# programs/CMakeLists.txt

# -------------------------------------------------------------------------
# We assume libmetis has installed an imported Metis::metis target,
# which already carries the proper include directories and link flags.
# -------------------------------------------------------------------------

# Helper to add and install a Metis-based tool
macro(add_metis_program NAME)
  # remaining ARGN are the source files
  add_executable(${NAME} ${ARGN})
  target_link_libraries(${NAME}
    PRIVATE Metis::metis 
  )
  if(METIS_INSTALL)
    install(TARGETS ${NAME}
      RUNTIME DESTINATION bin
    )
  endif()
endmacro()

# now add them one by one, with exactly the files that actually exist
add_metis_program(gpmetis
  gpmetis.c
  cmdline_gpmetis.c
  io.c
  stat.c
)

add_metis_program(ndmetis
  ndmetis.c
  cmdline_ndmetis.c
  io.c
  smbfactor.c
)

add_metis_program(mpmetis
  mpmetis.c
  cmdline_mpmetis.c
  io.c
  stat.c
)

add_metis_program(m2gmetis
  m2gmetis.c
  cmdline_m2gmetis.c
  io.c
)

add_metis_program(graphchk
  graphchk.c
  io.c
)

add_metis_program(cmpfillin
  cmpfillin.c
  io.c
  smbfactor.c
)