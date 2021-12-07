cmake_minimum_required(VERSION 3.15)
project(TEST_PROJECT)

add_executable(mytemplate
        ClangTest.cpp
        )

find_package(LLVM REQUIRED CONFIG)
find_package(Clang REQUIRED CONFIG)

add_definitions(${LLVM_DEFINITIONS})

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

target_include_directories(mytemplate
        PRIVATE
        ${LLVM_INCLUDE_DIRS}
        ${CLANG_INCLUDE_DIRS}
        )

if(NOT LLVM_ENABLE_RTTI)
    target_compile_options(mytemplate PRIVATE $<IF:$<CXX_COMPILER_ID:MSVC>,/GR-,-fno-rtti>)
endif()

llvm_map_components_to_libnames(LLVM_LIBS support)
set(CLANG_LIBS clangAST clangBasic clangFrontend clangTooling)

set_property(TARGET mytemplate PROPERTY CXX_STANDARD 17)

target_link_libraries(mytemplate
        PRIVATE
        ${LLVM_LIBS}
        ${CLANG_LIBS}
        )

include(CheckCXXCompilerFlag)
check_cxx_compiler_flag("-fvisibility-inlines-hidden" SUPPORT_FVIS_INLINES_HIDDEN)
if (${SUPPORT_FVIS_INLINES_HIDDEN})
    target_compile_options(mytemplate PRIVATE -fvisibility-inlines-hidden)
endif()