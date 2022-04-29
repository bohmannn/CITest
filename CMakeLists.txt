cmake_minimum_required(VERSION 3.2)

set(Boost_DEBUG ON)

cmake_policy(SET CMP0074 NEW)

project(CITest VERSION 0.0.1$ENV{TRAVIS_BUILD_NUMBER})


configure_file(version.h.in ${PROJECT_SOURCE_DIR}/version.h)

include_directories("C:/Program Files (x86)/spdlog/include")
link_directories("C:/Program Files (x86)/spdlog/lib")
link_directories("C:/Program Files/boost/boost_1_78_0/stage/lib")

add_executable(helloworld_cli main.cpp)

add_library(helloworld lib.cpp)

add_executable(test_version test_version.cpp)


set (Boost_USE_STATIC_LIBS ON)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)
find_package(spdlog REQUIRED)
find_package(Threads REQUIRED)
find_package(Boost COMPONENTS unit_test_framework REQUIRED)
#find_package(Boost COMPONENTS program_options REQUIRED)


set_target_properties(helloworld_cli helloworld test_version PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON

)

set_target_properties(test_version PROPERTIES
    #COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
    INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR}

)

target_link_libraries(helloworld_cli
    helloworld
    Threads::Threads
	spdlog
)

target_link_libraries(test_version
    ${Boost_LIBRARIES}
    helloworld
)

install(TARGETS helloworld_cli RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT shebdim@gmail.com)

set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)

include(CPack)

enable_testing()

add_test(helloworld_tests test_version)


if (CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
    target_compile_options(test_version PRIVATE /W4 /w44265 /w44061 /w44062 )
endif()