cmake_minimum_required (VERSION 2.8)

project (peg C)

list (APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/.cmake")

include (CheckSymbolExists)
include (CheckCSourceCompiles)
include (GNUInstallDirs)

if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
  set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c99 -Wall -Wextra -pedantic")
endif ()

list (APPEND CMAKE_REQUIRED_DEFINITIONS
  -D_GNU_SOURCE
  -D_CRT_RAND_S
  -DVC_EXTRALEAN
  -DWIN32_LEAN_AND_MEAN)

check_symbol_exists (getopt stdlib.h HAVE_GETOPT)

set (SOURCES)

if (HAVE_GETOPT)
else ()
  list (APPEND SOURCES win/getopt.c)
  include_directories (win)
endif ()

list (APPEND SOURCES 
    src/compile.c
    src/leg.c
    src/tree.c)

add_executable (leg ${SOURCES})

set (CMAKE_REQUIRED_DEFINITIONS -fvisibility=hidden)
check_c_source_compiles (
  "__attribute__((visibility(\"default\"))) int main(void) { return 0; }"
  CC_HAVE_VISIBILITY)
set (CMAKE_REQUIRED_DEFINITIONS)

install(TARGETS leg
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})
