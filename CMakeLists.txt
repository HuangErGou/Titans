cmake_minimum_required(VERSION 3.5)
project(Titans)

include(CMakeOptions.txt)

set(CMAKE_CXX_STANDARD 11)

# create install directory
execute_process(COMMAND ${CMAKE_COMMAND} -E make_directory
    ${CMAKE_CURRENT_BINARY_DIR}/install_dir RESULT_VARIABLE result)

if(result)
    message(FATAL_ERROR "Create install dir failed: ${result}")
endif()

# ======================================================================================================

if (WITH_JSONCPP)
    # create jsoncpp build directory
    execute_process(COMMAND ${CMAKE_COMMAND} -E make_directory
        ${CMAKE_CURRENT_BINARY_DIR}/jsoncpp_build RESULT_VARIABLE result)
    if(result)
        message(FATAL_ERROR "Create install dir failed: ${result}")
    endif()

    # Generate jsoncpp makefile
    execute_process(COMMAND ${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_BINARY_DIR}/jsoncpp_build
        ${CMAKE_COMMAND} -DCMAKE_BUILD_TYPE=Release
        -DJSONCPP_WITH_TESTS=OFF
        -DJSONCPP_WITH_POST_BUILD_UNITTEST=OFF
        -DJSONCPP_WITH_PKGCONFIG_SUPPORT=OFF
        -DBUILD_STATIC_LIBS=ON
        -DBUILD_SHARED_LIBS=OFF
        -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/install_dir
        -G "Unix Makefiles"
        ${CMAKE_CURRENT_SOURCE_DIR}/jsoncpp/jsoncpp-0.10.7)

    if (result)
        message(FATAL_ERROR "Build step for jsoncpp Generate jsoncpp makefile failed: ${result}")
    endif ()

    # build jsoncpp
    execute_process(COMMAND ${CMAKE_COMMAND} --build ${CMAKE_CURRENT_BINARY_DIR}/jsoncpp_build
        RESULT_VARIABLE result
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/jsoncpp/jsoncpp-0.10.7)
    if (result)
        message(FATAL_ERROR "Build step for jsoncpp failed: ${result}")
    endif()

    execute_process(COMMAND
        ${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_BINARY_DIR}/jsoncpp_build
        make -j RESULT_VARIABLE result)
    if (result)
        message(FATAL_ERROR "Build step for jsoncpp install failed: ${result}")
    endif ()

    execute_process(COMMAND
        ${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_BINARY_DIR}/jsoncpp_build
        make install RESULT_VARIABLE result)
    if (result)
        message(FATAL_ERROR "Build step for jsoncpp install failed: ${result}")
    endif ()
endif() # WITH_JSONCPP

# ======================================================================================================

if (WITH_OPENSSL)
    # create openssl build directory
    execute_process(COMMAND ${CMAKE_COMMAND} -E make_directory
        ${CMAKE_CURRENT_BINARY_DIR}/openssl_build RESULT_VARIABLE result)
    if(result)
        message(FATAL_ERROR "Create install dir failed: ${result}")
    endif()

    execute_process(COMMAND
        ${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_BINARY_DIR}/openssl_build
        ${CMAKE_CURRENT_SOURCE_DIR}/openssl/openssl-OpenSSL_1_1_1b/config
        --prefix=${CMAKE_CURRENT_BINARY_DIR}/install_dir RESULT_VARIABLE result)
    if (result)
        message(FATAL_ERROR "Build step for openssl install failed: ${result}")
    endif ()

    execute_process(COMMAND
        ${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_BINARY_DIR}/openssl_build
        make -j RESULT_VARIABLE result)
    if (result)
        message(FATAL_ERROR "Build step for openssl install failed: ${result}")
    endif ()

    execute_process(COMMAND
        ${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_BINARY_DIR}/openssl_build
        make install RESULT_VARIABLE result)
    if (result)
        message(FATAL_ERROR "Build step for openssl install failed: ${result}")
    endif ()
endif() # WITH_OPENSSL

# ======================================================================================================
