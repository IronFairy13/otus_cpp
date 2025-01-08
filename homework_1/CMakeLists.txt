cmake_minimum_required(VERSION 3.2)

set(PATCH_VERSION "1" CACHE INTERNAL "Patch version")
set(PROJECT_VERSION 0.0${PATCH_VERSION})


project(homework_1 VERSION ${PROJECT_VERSION})

configure_file(version.h.in version.h)


add_executable(homework_1 main.cpp)
add_library(homework_1 lib.cpp)

set_target_properties(homework_1_cli homework_1 PROPERTIES
    CXX_STANDARD 14
    CXX_STANDART_REQUIRED ON
)

target_include_directories(homework_1
    PRIVATE "${CMAKE_BINARY_DIR}"
)

target_link_libraries(homework_1_cli PRIVATE "${CMAKE_BINARY_DIR}"
    homework_1
)

# Настройка опций компиляции
if(MSVC)
    target_compile_options(helloworld_cli PRIVATE /W4)
    target_compile_options(helloworld PRIVATE /W4)
else()
    target_compile_options(helloworld_cli PRIVATE -Wall -Wextra -pedantic -Werror)
    target_compile_options(helloworld PRIVATE -Wall -Wextra -pedantic -Werror)
endif()

# Подключение GoogleTest
include(FetchContent)
FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/refs/tags/release-1.13.0.zip
)

FetchContent_MakeAvailable(googletest)

enable_testing()

add_executable(test test_version.cpp)

target_link_libraries(test PRIVATE
    gtest
    gtest_main
    helloworld
)

if(MSVC)
    target_compile_options(test PRIVATE /W4)
else()
    target_compile_options(test PRIVATE -Wall -Wextra -pedantic -Werror)
endif()

add_test(NAME test COMMAND test)

install (TARGETS homework_1_cli RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)
set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKEGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")
set(CPACK_PACKAGE_CONTACT savchenko.mn.dev@gmail.com)
include(CPack)