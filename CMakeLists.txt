cmake_minimum_required(VERSION 3.1)

set(project_name example-libressl) ## rename your project here

project(${project_name}-loader)

include(ExternalProject)

#############################################################################
## load all dependencies

ExternalProject_Add(oatpp
        GIT_REPOSITORY "https://github.com/oatpp/oatpp.git"
        GIT_TAG origin/master
        CMAKE_ARGS -DOATPP_BUILD_TESTS=OFF -DBUILD_SHARED_LIBS=${BUILD_SHARED_LIBS}
)

ExternalProject_Add(oatpp-libressl
        GIT_REPOSITORY "https://github.com/oatpp/oatpp-libressl.git"
        GIT_TAG origin/master
        CMAKE_ARGS -DBUILD_SHARED_LIBS=${BUILD_SHARED_LIBS}
        DEPENDS oatpp
)

ExternalProject_Add(main
        SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/main
        INSTALL_COMMAND cmake -E echo "SKIP INSTALL"
        CMAKE_ARGS -DBUILD_SHARED_LIBS=${BUILD_SHARED_LIBS}
        DEPENDS oatpp oatpp-libressl
)

#############################################################################
## make run command

ExternalProject_Get_Property(main BINARY_DIR)

add_custom_target(run
        COMMAND ${BINARY_DIR}/${project_name}-exe
        DEPENDS main
        WORKING_DIRECTORY ${BINARY_DIR}
)

#############################################################################
## make test command

enable_testing()
add_test(all-tests ${BINARY_DIR}/${project_name}-test)