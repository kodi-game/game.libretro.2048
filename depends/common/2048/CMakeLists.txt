cmake_minimum_required(VERSION 3.1)
project(2048)

list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR})

include(ExternalProject)

if(CMAKE_BUILD_TYPE MATCHES Debug)
  set(LIBRETRO_DEBUG DEBUG=1)
endif()

if("${CORE_SYSTEM_NAME}" STREQUAL "windows")
  find_package(MinGW REQUIRED)
  set(BUILD_COMMAND ${MINGW_MAKE} -f Makefile.libretro platform=win ${LIBRETRO_DEBUG})
elseif("${CORE_SYSTEM_NAME}" STREQUAL "linux")
  set(BUILD_COMMAND make -f Makefile.libretro ${LIBRETRO_DEBUG})
elseif("${CORE_SYSTEM_NAME}" STREQUAL "darwin" OR "${CORE_SYSTEM_NAME}" STREQUAL "osx")
  set(BUILD_COMMAND make -f Makefile.libretro platform=osx ${LIBRETRO_DEBUG})
elseif("${CORE_SYSTEM_NAME}" STREQUAL "ios")
  # TODO: Should we use platform=osx?
  message(FATAL_ERROR "${PROJECT_NAME} needs IOS build command in CMakeLists.txt!")
elseif("${CORE_SYSTEM_NAME}" STREQUAL "android")
  # TODO: This only works for android-armv7
  set(BUILD_COMMAND make -f Makefile.libretro ${LIBRETRO_DEBUG})
  # For other architectures, use:
  # - android-x86_64
  # - android-x86
  # - android-armeabi
  # - android-armeabi_v7a
  # - android-arm64_v8a
  # - android-mips
elseif("${CORE_SYSTEM_NAME}" STREQUAL "rbpi")
  set(BUILD_COMMAND make -f Makefile.libretro ${LIBRETRO_DEBUG})
elseif("${CORE_SYSTEM_NAME}" STREQUAL "freebsd")
  set(BUILD_COMMAND make -f Makefile.libretro platform=bsd ${LIBRETRO_DEBUG})
else()
  message(FATAL_ERROR "${PROJECT_NAME} - Unknown system: ${CORE_SYSTEM_NAME}")
endif()

externalproject_add(2048
                    SOURCE_DIR ${PROJECT_SOURCE_DIR}
                    PREFIX 2048
                    CONFIGURE_COMMAND ""
                    INSTALL_COMMAND ""
                    BUILD_COMMAND ${BUILD_COMMAND}
                    BUILD_IN_SOURCE 1)

#install the generated shared library
install(FILES ${PROJECT_SOURCE_DIR}/2048_libretro${CMAKE_SHARED_LIBRARY_SUFFIX}
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)

# write the fceu-next-config.cmake script
file(WRITE ${CMAKE_INSTALL_PREFIX}/libretro-2048-config.cmake "set(LIBRETRO_2048_LIB ${CMAKE_INSTALL_PREFIX}/lib/2048_libretro${CMAKE_SHARED_LIBRARY_SUFFIX})")
