cmake_minimum_required(VERSION 3.16)
project(ftplibpp
    VERSION 2.0.6 # 240509
    LANGUAGES CXX C
)

#  ============ Configure Stage ============
find_package(OpenSSL COMPONENTS Crypto)

# make cache variables for install destinations
include(GNUInstallDirs)

add_library(ftplibpp SHARED 
  src/ftplib.cpp
)

target_sources(ftplibpp INTERFACE
    FILE_SET HEADERS 
    FILES inc/ftplib.h
)

target_include_directories(ftplibpp INTERFACE
                           "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>/inc"
                           "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>/inc"
        ${OPENSSL_INCLUDE_DIR}
)

target_include_directories(ftplibpp PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/inc)

target_link_libraries(ftplibpp INTERFACE
        ${OPENSSL_CRYPTO_LIBRARIES}
        ${OPENSSL_SSL_LIBRARIES}
)

# ============ Install Stage ============

include(GNUInstallDirs)

set(INCLUDE_INSTALL_DIR ${CMAKE_INSTALL_INCLUDEDIR}
    CACHE PATH "Location of header files" )
set(SYSCONFIG_INSTALL_DIR ${CMAKE_INSTALL_SYSCONFDIR}/ftplibpp
    CACHE PATH "Location of configuration files" )
get_target_property(LINK_LIBS ftplibpp LINK_LIBS)
set(LIBRARIES_TO_LINK ${LINK_LIBS} CACHE PATH "Libraries to link against")
#...
# install header
install(TARGETS ftplibpp
        EXPORT ftplibppTargets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION inc
        FILE_SET HEADERS
        )

include(CMakePackageConfigHelpers)
configure_package_config_file(cmake/ftplibppConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/ftplibppConfig.cmake
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ftplibpp
  PATH_VARS INCLUDE_INSTALL_DIR SYSCONFIG_INSTALL_DIR)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/ftplibppConfigVersion.cmake
  VERSION ${PACKAGE_VERSION}
  COMPATIBILITY AnyNewerVersion)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/ftplibppConfig.cmake
              ${CMAKE_CURRENT_BINARY_DIR}/ftplibppConfigVersion.cmake
   DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ftplibpp )

#message(FATAL_ERROR "${INCLUDE_INSTALL_DIR}")
