cmake_minimum_required(VERSION 2.8.11)
project(conan_termcap C)

include("${CMAKE_BINARY_DIR}/conanbuildinfo.cmake")
conan_basic_setup()

include(CheckIncludeFile)
include(GNUInstallDirs)

set(TERMCAP_SOURCES "" CACHE STRING "Sources of termcap")
set(TERMCAP_HEADERS "" CACHE STRING "Headers of termcap")
set(TERMCAP_INC_OPTS "" CACHE STRING "Optional includes of termcap")

foreach(TERMCAP_INC_OPT ${TERMCAP_INC_OPTS})
    string(TOUPPER "${TERMCAP_INC_OPT}" _var)
    string(REPLACE "." "_" _var "${_var}")
    set(_var "HAVE_${_var}")
    CHECK_INCLUDE_FILE("${TERMCAP_INC_OPT}" "${_var}")
    if(${_var})
        add_definitions("-D${_var}")
    endif()
endforeach()

add_definitions(-DSTDC_HEADERS)
add_definitions(-DTERMCAP_BUILDING)
if(BUILD_SHARED_LIBS)
    add_definitions(-DTERMCAP_SHARED)
endif()
if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

add_library(termcap ${TERMCAP_SOURCES} ${TERMCAP_HEADERS})
install(TARGETS termcap
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
)
install(FILES ${TERMCAP_HEADERS} DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")
