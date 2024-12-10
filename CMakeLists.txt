#=============================================================================#
#============================== Project ======================================#
#=============================================================================#
cmake_minimum_required(VERSION 3.12)
project(
    Htool
    VERSION 0.9.0
    DESCRIPTION "Example of cpp project."
    HOMEPAGE_URL "https://github.com/PierreMarchand20/CppNewProject"
    LANGUAGES CXX)

if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
    # To set default CMAKE_BUILD_TYPE
    set(default_build_type "Release")
    if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
        message(STATUS "Setting build type to '${default_build_type}' as none was specified.")
        set(CMAKE_BUILD_TYPE
            "${default_build_type}"
            CACHE STRING "Choose the type of build." FORCE)
    endif()

    message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")

    # Sanitizers
    include(cmake-scripts/sanitizers.cmake)

    # Formatting
    include(cmake-scripts/formatting.cmake)
    file(
        GLOB_RECURSE
        ALL_CODE_FILES
        ${PROJECT_SOURCE_DIR}/include/*.[h]pp
        ${PROJECT_SOURCE_DIR}/include/*.[h]
        ${PROJECT_SOURCE_DIR}/examples/*.[ch]pp
        ${PROJECT_SOURCE_DIR}/examples/*.[ch]
        ${PROJECT_SOURCE_DIR}/tests/*.[ch]pp
        ${PROJECT_SOURCE_DIR}/tests/*.[ch])

    clang_format(format ${ALL_CODE_FILES})
    file(GLOB_RECURSE CMAKE_FILES ${PROJECT_SOURCE_DIR}/CMakeLists.txt)
    cmake_format(cmake_format ${CMAKE_FILES})

    # Files to do find_package for some module
    list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake_modules")

    # Information about compilation exported
    set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

    # Let's nicely support folders in IDE's
    set_property(GLOBAL PROPERTY USE_FOLDERS ON)

    # Testing only available if this is the main app
    # Note this needs to be done in the main CMakeLists
    # since it calls enable_testing, which must be in the
    # main CMakeLists.
    include(CTest)

    option(CPP_NEW_PROJECT_HARDENING_TESTS "Add -Werror for testing" OFF)
endif()

#=============================================================================#
#========================== External Libraries ===============================#
#=============================================================================#
# BLAS
find_package(BLAS REQUIRED)
message("-- Found Blas implementation:" "${BLAS_LIBRARIES}")

# LAPACK
find_package(LAPACK REQUIRED)
message("-- Found Lapack:" "${LAPACK_LIBRARIES}")

# OPENMP
find_package(OpenMP)

#=============================================================================#
#========================== Libraries ========================================#
#=============================================================================#
#=== CppNewProject
add_library(cpp_new_project INTERFACE)
target_include_directories(cpp_new_project INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include> $<INSTALL_INTERFACE:include>)
target_link_libraries(cpp_new_project INTERFACE ${BLAS_LIBRARIES} ${LAPACK_LIBRARIES})
if(OpenMP_CXX_FOUND)
    target_link_libraries(cpp_new_project INTERFACE OpenMP::OpenMP_CXX)
endif()
target_compile_features(cpp_new_project INTERFACE cxx_std_20)

#=============================================================================#
#========================== Repertories ======================================#
#=============================================================================#
add_subdirectory(src)

# Add tests
if((CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME) AND BUILD_TESTING)
    if(CODE_COVERAGE AND (CMAKE_C_COMPILER_ID MATCHES "GNU" OR CMAKE_CXX_COMPILER_ID MATCHES "GNU"))
        target_compile_options(cpp_new_project INTERFACE -fprofile-arcs -ftest-coverage)
        target_link_libraries(cpp_new_project INTERFACE gcov)
    endif()
    if(CPP_NEW_PROJECT_HARDENING_TESTS)
        target_compile_options(cpp_new_project INTERFACE -Werror)
    endif()
    target_compile_options(
        cpp_new_project
        INTERFACE -Wall
                  -Wextra
                  -Wshadow
                  -Wnon-virtual-dtor
                  -pedantic
                  -Wold-style-cast
                  -Wcast-align
                  -Wunused
                  -Woverloaded-virtual
                  -Wconversion
                  -Wsign-conversion
                  -Wdouble-promotion
                  -Wno-sign-compare
                  -Wextra-semi
                  -Wzero-as-null-pointer-constant
                  -Wundef)
    if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
        target_compile_options(cpp_new_project INTERFACE -Wimplicit-fallthrough -Wextra-semi-stmt -ferror-limit=200)
    elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
        target_compile_options(
            cpp_new_project
            INTERFACE -Wmisleading-indentation
                      -Wduplicated-cond
                      -Wduplicated-branches
                      -Wlogical-op
                      -Wnull-dereference
                      -Wuseless-cast
                      -fmax-errors=200)
    endif()
    add_custom_target(build-tests)
    add_subdirectory(tests)
endif()
