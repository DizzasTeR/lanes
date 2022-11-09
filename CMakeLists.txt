PROJECT(lanes C)
CMAKE_MINIMUM_REQUIRED(VERSION 3.0)

# Build
file(GLOB LANES_SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/**)
add_library(lanes STATIC ${LANES_SRC})

target_include_directories(lanes PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/src)

if(USE_PTHREAD)
	add_compile_definitions(-DHAVE_WIN32_PTHREAD)
endif(USE_PTHREAD)
#2DO - patch threading.c to suppot cygwin.
# The following values are just a guess.
# WARNING: test segfault under Cygwin
if(CYGWIN)
	add_compile_definitions(-D_PRIO_MODE=SCHED_FIFO)
	add_compile_definitions(-D_PRIO_HI=15) # maximum that doesn't crash
	add_compile_definitions(-D_PRIO_0=0)
	add_compile_definitions(-D_PRIO_LO=-15) # ???
	add_compile_definitions(-Dpthread_yield=sched_yield)
endif(CYGWIN)

set(LIBS "")
if(UNIX AND NOT CYGWIN)
  SET(LIBS pthread)
endif(UNIX AND NOT CYGWIN)

target_link_libraries(lanes PRIVATE lua::lib ${LIBS})
set_target_properties(lanes PROPERTIES PREFIX "")