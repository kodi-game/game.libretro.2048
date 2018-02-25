cmake_minimum_required(VERSION 3.1)
project(2048)

list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR})

string(TOUPPER ${PROJECT_NAME} PROJECT_NAME_UC)

set(LIBRETRO_BINARY_DIR .)
set(LIBRETRO_SONAME 2048_libretro${CMAKE_SHARED_LIBRARY_SUFFIX})
set(LIBRETRO_JNISONAME libretro${CMAKE_SHARED_LIBRARY_SUFFIX})

if(CMAKE_BUILD_TYPE MATCHES Debug)
  set(LIBRETRO_DEBUG DEBUG=1)
endif()

add_library(${PROJECT_NAME}_libretro SHARED
  game.h
  game_noncairo.c
  game_shared.c
  game_shared.h
  libretro.c
  libretro.h
)
install(TARGETS ${PROJECT_NAME}_libretro EXPORT ${PROJECT_NAME}_libretro RUNTIME DESTINATION lib ARCHIVE DESTINATION lib)
if(MSVC)
  install(FILES $<TARGET_PDB_FILE:${PROJECT_NAME}_libretro> DESTINATION lib CONFIGURATIONS Debug RelWithDebInfo)
endif()

# write the config.cmake script
file(WRITE ${CMAKE_INSTALL_PREFIX}/libretro-${PROJECT_NAME}-config.cmake "set(${PROJECT_NAME_UC}_LIB ${CMAKE_INSTALL_PREFIX}/lib/${LIBRETRO_SONAME})")
