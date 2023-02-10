cmake_minimum_required(VERSION 3.24)
project(Project1)

set(CMAKE_CXX_STANDARD 14)


#recommended by aman to match test environment
set(GCC_COVERAGE_COMPILE_FLAGS "-Wall -Werror")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS}" )

#pull in desired version of catch through cmake automatically, make it available
Include(FetchContent)
FetchContent_Declare(
        Catch2
        GIT_REPOSITORY https://github.com/catchorg/Catch2.git
        GIT_TAG        v3.0.1 # or a later release
)
FetchContent_MakeAvailable(Catch2)

include_directories(test-unit)

#the first word is the name of the executble
# everything else are the files to include - yours will probably look a bit different than mine
add_executable(Main
        src/main.cpp # your main
        src/AVL.cpp src/AVL.h src/Parser.cpp src/Parser.h src/StudentNode.h)

#this one doesn't include main, instead having your test.cpp
add_executable(Tests
        test-unit/test.cpp # your test file
        src/AVL.cpp src/AVL.h src/Parser.cpp src/Parser.h src/StudentNode.h)
target_link_libraries(Tests PRIVATE Catch2::Catch2WithMain) #link catch to test.cpp file