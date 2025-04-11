cmake_minimum_required(VERSION 3.14)
project(psa CXX)

# ---- Source files ----
set(SRC_DIR src)
set(SOURCES
    ${SRC_DIR}/main.cpp
    ${SRC_DIR}/analysis.cpp
    ${SRC_DIR}/config.cpp
    ${SRC_DIR}/curve.cpp
    ${SRC_DIR}/delaunay.cpp
    ${SRC_DIR}/image.cpp
    ${SRC_DIR}/param.cpp
    ${SRC_DIR}/periodogram.cpp
    ${SRC_DIR}/point.cpp
    ${SRC_DIR}/result.cpp
    ${SRC_DIR}/spectrum.cpp
    ${SRC_DIR}/statistics.cpp
)



# run brew install cairo
find_package(PkgConfig REQUIRED)
pkg_check_modules(CAIRO REQUIRED cairo)
# print a message when cairo is found
message(STATUS "Cairo include dirs: ${CAIRO_INCLUDE_DIRS}")
# Use the first include directory only
list(GET CAIRO_INCLUDE_DIRS 0 CAIRO_INCLUDE_DIR)
# get parent dir
get_filename_component(CAIRO_DIR "${CAIRO_INCLUDE_DIR}" DIRECTORY)
# append to CAIRO_INCLUDE_DIRS
list(APPEND CAIRO_INCLUDE_DIRS "${CAIRO_DIR}")
message(STATUS "Cairo dir: ${CAIRO_DIR}")
message(STATUS "Cairo include dirs: ${CAIRO_INCLUDE_DIRS}")
message(STATUS "Cairo libraries: ${CAIRO_LIBRARIES}")
# Get the first path from CAIRO_LIBRARY_DIRS
list(GET CAIRO_LIBRARY_DIRS 0 CAIRO_LIBRARY_DIR)

# make an alias cairo::cairo
add_library(cairo::cairo INTERFACE IMPORTED)
set_target_properties(cairo::cairo PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES "${CAIRO_INCLUDE_DIRS}"
    INTERFACE_LINK_DIRECTORIES "${CAIRO_LIBRARY_DIR}"
    INTERFACE_LINK_LIBRARIES "${CAIRO_LIBRARIES}"
)





message(STATUS "CAIRO libraries: ${CAIRO_LIBRARIES}")
message(STATUS "CAIRO library dirs: ${CAIRO_LIBRARY_DIRS}")

# even if you do brew install libomp, you still need a Monstrousity like this
if(APPLE)
    set(CMAKE_C_COMPILER clang)
    set(CMAKE_CXX_COMPILER clang++)
    set(OpenMP_ROOT "/opt/homebrew/opt/libomp")
    if(CMAKE_C_COMPILER_ID MATCHES "Clang\$")
        set(OpenMP_C_FLAGS "-Xpreprocessor -fopenmp")
        set(OpenMP_C_LIB_NAMES "omp")
        set(OpenMP_omp_LIBRARY omp)
        set(OpenMP_C_INCLUDE_DIR "${OpenMP_ROOT}/include") # add this!
        # and library directory
    endif()

    if(CMAKE_CXX_COMPILER_ID MATCHES "Clang\$")
        set(OpenMP_CXX_FLAGS "-Xpreprocessor -fopenmp")
        set(OpenMP_CXX_LIB_NAMES "omp")
        set(OpenMP_omp_LIBRARY omp)
        set(OpenMP_CXX_INCLUDE_DIR "${OpenMP_ROOT}/include") # add this!
    endif()
    # this sucks.
    set(OpenMP_omp_LIBRARY "${OpenMP_ROOT}/lib/libomp.dylib")
endif()

find_package(OpenMP COMPONENTS CXX REQUIRED)

add_executable(psa ${SOURCES})
target_link_libraries(psa PRIVATE OpenMP::OpenMP_CXX cairo::cairo)
