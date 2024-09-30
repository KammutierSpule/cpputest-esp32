# https://github.com/espressif/esp-idf/blob/v5.3.1/examples/build_system/cmake/import_lib/components/tinyxml2/CMakeLists.txt

idf_component_register()
include(ExternalProject)

set(CPPUTEST_INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}/cpputest_install)

externalproject_add(cpputest_proj
    URL https://github.com/cpputest/cpputest/releases/download/latest-passing-build/cpputest-latest.tar.gz

    CMAKE_ARGS
        -DCMAKE_TOOLCHAIN_FILE=${CMAKE_TOOLCHAIN_FILE}

        -DCPPUTEST_BUILD_TESTING=OFF
        -DCPPUTEST_EXAMPLES=OFF
        -DCPPUTEST_USE_LONG_LONG=ON

        # Pass the install directory to the subproject.
        -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR>

    USES_TERMINAL_DOWNLOAD TRUE
    USES_TERMINAL_CONFIGURE TRUE
    USES_TERMINAL_BUILD TRUE

    INSTALL_DIR ${CPPUTEST_INSTALL_DIR}
    BUILD_BYPRODUCTS "${CPPUTEST_INSTALL_DIR}/lib/libcpputest.a"
)

add_prebuilt_library(cpputest_lib "${CPPUTEST_INSTALL_DIR}/lib/libcpputest.a"
                     PRIV_REQUIRES cxx)
target_include_directories(cpputest_lib INTERFACE "${CPPUTEST_INSTALL_DIR}/include")
add_dependencies(cpputest_lib cpputest_proj)

target_link_libraries(${COMPONENT_LIB} INTERFACE cpputest_lib)
