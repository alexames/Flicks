cmake_minimum_required(VERSION 3.27)
project(
    flicks
    VERSION 1.0.0
    DESCRIPTION "A unit of time defined in C++."
    HOMEPAGE_URL "https://github.com/alexames/flicks"
    LANGUAGES CXX
)

include(CMakePackageConfigHelpers)
include(GNUInstallDirs)

add_library(flicks INTERFACE)
add_library(flicks::flicks ALIAS flicks)

target_include_directories(
    flicks
    INTERFACE
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
        "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)

set(package_name         "flicks")
set(cmake_install_dir    "${CMAKE_INSTALL_LIBDIR}/cmake/${package_name}")
set(version_config       "${PROJECT_BINARY_DIR}/${package_name}-config-version.cmake")
set(project_config       "${PROJECT_BINARY_DIR}/${package_name}-config.cmake")
set(targets_export_name  "${package_name}-targets")
set(targets_file         "${targets_export_name}.cmake")

write_basic_package_version_file(
    ${version_config}
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
    "${PROJECT_SOURCE_DIR}/cmake/${package_name}-config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${cmake_install_dir}"
)

# install header
install(
    FILES "${CMAKE_CURRENT_LIST_DIR}/flicks.h"
    DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

# install project config file and config version file
install(
    FILES
        "${project_config}"
        "${version_config}"
    DESTINATION "${cmake_install_dir}"
)

# Install targets to an export set
install(
    TARGETS ${package_name}
    EXPORT ${targets_export_name}
    INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

# Install the export set to enable importing targets from the build tree
export(
    EXPORT ${targets_export_name}
    FILE ${targets_file}
    NAMESPACE ${package_name}::
)

# Install the export set to enable importing targets from the install tree
install(
    EXPORT ${targets_export_name}
    FILE ${targets_file}
    NAMESPACE ${package_name}::
    DESTINATION "${cmake_install_dir}"
)
