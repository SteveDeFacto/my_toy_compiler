cmake_minimum_required (VERSION 3.1.3)

project(Phoenix)

find_package(FLEX REQUIRED)
find_package(BISON REQUIRED)

set(EXECUTABLE_OUTPUT_PATH "./bin")
set(CMAKE_CXX_FLAGS "-std=c++11")
add_definitions( -D__STDC_LIMIT_MACROS -D__STDC_FORMAT_MACROS -D__STDC_CONSTANT_MACROS )


BISON_TARGET(MyParser ./src/parser.y ./src/parser.cpp)
FLEX_TARGET(MyScanner ./src/tokens.l ./src/tokens.cpp)
ADD_FLEX_BISON_DEPENDENCY(MyScanner MyParser)

include_directories( "./include" )

add_executable( Phoenix ./src/main.cpp ./src/codegen.cpp ./src/corefn.cpp ./src/main.cpp ./src/native.cpp ${BISON_MyParser_OUTPUTS} ${FLEX_MyScanner_OUTPUTS})

find_package(LLVM REQUIRED)
 
if( NOT LLVM_FOUND )
	message(FATAL_ERROR "LLVM package can't be found. Set CMAKE_PREFIX_PATH variable to LLVM's installation prefix.")
endif()
 
include_directories( ${LLVM_INCLUDE_DIRS} )
link_directories( ${LLVM_LIBRARY_DIRS} )
 
llvm_map_components_to_libnames(REQ_LLVM_LIBRARIES jit native)
 
target_link_libraries(Phoenix ${REQ_LLVM_LIBRARIES}) 


