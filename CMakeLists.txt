cmake_minimum_required(VERSION 3.21)
project(METIS VERSION 5.3.0 LANGUAGES C)

# -----------------------------------------------------------------------------
# 1) Expose the same options that your old Makefile used:
# -----------------------------------------------------------------------------
option(IDX64      "enable 64‑bit indices (IDXTYPEWIDTH=64)"   OFF)
option(REAL64     "enable 64‑bit reals   (REALTYPEWIDTH=64)" OFF)
option(SHARED     "build shared library"                     OFF)
option(GDB        "enable gdb symbols"                      OFF)
option(ASSERT     "enable ASSERT"                           OFF)
option(ASSERT2    "enable ASSERT2"                          OFF)
option(DEBUG      "enable DEBUG"                            OFF)
option(GPROF      "enable gprof profiling"                  OFF)
option(VALGRIND   "enable valgrind instrumentation"         OFF)
option(OPENMP     "enable OpenMP support"                   OFF)

find_package( GKlib REQUIRED)


option(BUILD_SHARED_LIBS "Build shared Metis libraries"       ${SHARED})
# Installation toggle (MSVC disables install by default)
if(MSVC)
  set(METIS_INSTALL FALSE)
else()
  option(METIS_INSTALL "Enable installation of targets"       ON)
endif()

# Path to an existing GKlib installation (if any)
set(GKLIB_PATH "" CACHE PATH "Path to GKlib install prefix")

# -----------------------------------------------------------------------------
#  Compute compile-time widths
# -----------------------------------------------------------------------------
set(_METIS_IDXWIDTH   32)
if(IDX64)
  set(_METIS_IDXWIDTH 64)
endif()

set(_METIS_REALWIDTH  32)
if(REAL64)
  set(_METIS_REALWIDTH 64)
endif()

# -----------------------------------------------------------------------------
#  Generate xinclude directory and headers
# -----------------------------------------------------------------------------
set(XINCLUDE_DIR "${CMAKE_BINARY_DIR}/xinclude")
file(MAKE_DIRECTORY ${XINCLUDE_DIR})

# metis.h.in should define @ _METIS_IDXWIDTH @ and @ _METIS_REALWIDTH @ at top
configure_file(
  "${CMAKE_SOURCE_DIR}/include/metis.h.in"
  "${XINCLUDE_DIR}/metis.h"
  @ONLY
)

# copy the CMakeLists.txt stub into xinclude
configure_file(
  "${CMAKE_SOURCE_DIR}/include/CMakeLists.txt"
  "${XINCLUDE_DIR}/CMakeLists.txt"
  COPYONLY
)

# make sure generated headers are found first
include_directories(BEFORE ${XINCLUDE_DIR})

# -----------------------------------------------------------------------------
#  GKlib include/link paths (if provided)
# -----------------------------------------------------------------------------
if(GKLIB_PATH)
  include_directories(${GKLIB_PATH}/include)
  link_directories(${GKLIB_PATH}/lib)
endif()

# -----------------------------------------------------------------------------
#  Add subdirectories for core library and programs
# -----------------------------------------------------------------------------
add_subdirectory(libmetis)
add_subdirectory(programs)

# -----------------------------------------------------------------------------
#  Export and install for find_package
# -----------------------------------------------------------------------------
set(ConfigPackageLocation "lib/cmake/Metis")

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/MetisConfigVersion.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
  "${CMAKE_CURRENT_LIST_DIR}/cmake/MetisConfig.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/MetisConfig.cmake"
  INSTALL_DESTINATION ${ConfigPackageLocation}
)

install(
  EXPORT MetisTargets
  NAMESPACE Metis::
  DESTINATION ${ConfigPackageLocation}
)

install(
  FILES
    "${CMAKE_CURRENT_BINARY_DIR}/MetisConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/MetisConfigVersion.cmake"
  DESTINATION ${ConfigPackageLocation}
)

