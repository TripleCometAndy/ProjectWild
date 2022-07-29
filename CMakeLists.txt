cmake_minimum_required(VERSION 3.20)

# set the project name
project(ProjectWild)

# specify the C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

#enable compiler warnings
if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang" OR CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_compile_options(ProjectWild PRIVATE -Wall -Wextra)
endif()

option(CK3E_BUILD_TESTS "Build tests" ON)

add_library(${PROJECT_NAME}Lib)
target_sources(${PROJECT_NAME}Lib PRIVATE
  src/main.cpp
)

target_include_directories(${PROJECT_NAME}Lib PUBLIC include sdlInclude/include)

file(COPY lib/SDL2.lib DESTINATION .)
file(COPY lib/SDL2main.lib DESTINATION .)
file(COPY lib/SDL2.dll DESTINATION .)

add_executable(${PROJECT_NAME})
target_sources(${PROJECT_NAME} PRIVATE
  src/main.cpp
)

target_link_libraries(${PROJECT_NAME} PRIVATE ${PROJECT_NAME}Lib SDL2.lib SDL2main.lib)

if(CK3E_BUILD_TESTS)
  enable_testing()

  include(FetchContent)

  FetchContent_Declare(
	googletest
	GIT_REPOSITORY https://github.com/TripleCometAndy/googletest
	GIT_TAG 0320f517fd920866d918e564105d68fd4362040a
  )

  FetchContent_MakeAvailable(googletest)

  add_executable(${PROJECT_NAME}Tests)
  target_sources(${PROJECT_NAME}Tests PRIVATE
    tests/src/main.cpp
  )
  
  target_include_directories(${PROJECT_NAME}Tests PRIVATE tests/include)

  target_link_libraries(${PROJECT_NAME}Tests PRIVATE ${PROJECT_NAME}Lib gtest_main gmock)
  add_test(NAME ${PROJECT_NAME}Tests COMMAND ${PROJECT_NAME}Tests)
endif()

include(InstallRequiredSystemLibraries)
install(FILES lib/SDL2.dll DESTINATION bin)
install(FILES images/hello_world.bmp DESTINATION bin)
install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION bin)
include(CPack)