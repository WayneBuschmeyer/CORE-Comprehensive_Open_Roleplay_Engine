cmake_minimum_required(VERSION 3.14)
project(CORE_Comprehensive_Open_Roleplay_Engine LANGUAGES CXX)

# 1. Set a modern C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 2. Let users (or CI) toggle test building on/off.
option(BUILD_TESTS "Build the test suite" ON)

# 3. Bring in Google Test automatically via FetchContent
include(FetchContent)

FetchContent_Declare(
  googletest
  URL https://github.com/google/googletest/archive/refs/tags/v1.14.0.zip
)
# Make the googletest content available
FetchContent_MakeAvailable(googletest)

# 4. If building tests, enable CTest
if (BUILD_TESTS)
  enable_testing()
endif()

#
# 5. Collect all source files in 'code/' automatically.
#    CONFIGURE_DEPENDS ensures CMake re-checks if you add/remove files.
#
file(GLOB_RECURSE CORE_SOURCE_FILES CONFIGURE_DEPENDS
  "${PROJECT_SOURCE_DIR}/code/*.cpp"
  "${PROJECT_SOURCE_DIR}/code/*.c"
)

#
# 6. Create the main executable with all code in 'code/'.
#
add_executable(core_engine
  ${CORE_SOURCE_FILES}
)

#
# 7. If tests are enabled, collect all test source files and create a test executable.
#
if (BUILD_TESTS)
  # 7a. Gather test files from unit & integration subfolders
  file(GLOB_RECURSE CORE_TEST_FILES CONFIGURE_DEPENDS
    "${PROJECT_SOURCE_DIR}/tests/unit/*.cpp"
    "${PROJECT_SOURCE_DIR}/tests/unit/*.c"
    "${PROJECT_SOURCE_DIR}/tests/integration/*.cpp"
    "${PROJECT_SOURCE_DIR}/tests/integration/*.c"
  )

  # 7b. Create one unified test executable
  add_executable(core_engine_tests
    ${CORE_TEST_FILES}
  )

  #
  # 7c. Link GoogleTest (downloaded via FetchContent).
  #     GTest::gtest_main = The main function provided by Google Test.
  #
  target_link_libraries(core_engine_tests
    PRIVATE
      GTest::gtest_main
  )

  #
  # 7d. Register this test executable with CTest so "ctest" can find and run it.
  #
  add_test(NAME core_engine_tests COMMAND core_engine_tests)
endif()
