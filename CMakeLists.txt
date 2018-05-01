cmake_minimum_required (VERSION 3.10)

set (CMAKE_CXX_COMPILER g++)
set (CMAKE_C_COMPILER gcc)
set (CMAKE_CXX_STANDARD 11)
set (CMAKE_CXX_STANDARD_REQUIRED ON)

project (Project)
enable_language (CXX)

set (CMAKE_EXPORT_COMPILE_COMMANDS ON) 
set (CMAKE_VERBOSE_MAKEFILE TRUE) 

set (SOURCE_FILES
	src/Template.cpp
	src/main.cpp
)

add_executable (Project ${SOURCE_FILES})

target_include_directories (Project PUBLIC include)

find_package (Boost 1.65.0 COMPONENTS "unit_test_framework" REQUIRED)

include_directories (
	${CMAKE_CURRENT_SOURCE_DIR}/include
	${Boost_INCLUDE_DIRS}
)

set (SOURCE_TEST_FILES
	test/master.cpp
	test/TestTemplate.cpp
	src/Template.cpp
)

add_executable (TestProject ${SOURCE_TEST_FILES})

target_include_directories (TestProject PUBLIC include)

target_link_libraries (TestProject
	${Boost_UNIT_TEST_FRAMEWORK_LIBRARY}
)

enable_testing ()

add_test (NAME Test COMMAND TestProject
	--report_level=detailed
	--log_level=all
	--color_output=yes
)

add_custom_target (check ${CMAKE_COMMAND} -E env CTEST_OUTPUT_ON_FAILURE=1 BOOST_TEST_LOG_LEVEL=all
	${CMAKE_CTEST_COMMAND} -C $<CONFIG> --verbose
	WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)
