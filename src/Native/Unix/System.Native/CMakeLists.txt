project(System.Native)

# We mark the function which needs exporting with DLLEXPORT
add_compile_options(-fvisibility=hidden)

set(NATIVE_SOURCES
    pal_console.cpp
    pal_errno.c
    pal_interfaceaddresses.c
    pal_io.c
    pal_maphardwaretype.c
    pal_memory.c
    pal_mount.cpp
    pal_networking.c
    pal_networkstatistics.c
    pal_process.cpp
    pal_random.c
    pal_runtimeinformation.cpp
    pal_runtimeextensions.c
    pal_signal.c
    pal_string.cpp
    pal_tcpstate.c
    pal_time.c
    pal_uid.cpp
    pal_datetime.c
    pal_sysctl.c
)

if (CMAKE_SYSTEM_NAME STREQUAL Linux)
    set(NATIVE_SOURCES ${NATIVE_SOURCES} pal_networkchange.cpp)

    if (!HAVE_LINUX_RTNETLINK_H)
        message(FATAL_ERROR "Could not find linux/rtnetlink.h")
    endif ()
endif ()

add_library(System.Native
    SHARED
    ${NATIVE_SOURCES}
    ${VERSION_FILE_PATH}
)


add_library(System.Native-Static
    STATIC
    ${NATIVE_SOURCES}
    ${VERSION_FILE_PATH}
)

# Disable the "lib" prefix and override default name
set_target_properties(System.Native-Static PROPERTIES PREFIX "")
set_target_properties(System.Native-Static PROPERTIES OUTPUT_NAME System.Native CLEAN_DIRECT_OUTPUT 1)

if (CMAKE_SYSTEM_NAME STREQUAL Linux AND NOT CLR_CMAKE_PLATFORM_ANDROID)
    target_link_libraries(System.Native rt)
endif ()

if (CMAKE_SYSTEM_NAME STREQUAL FreeBSD)
    target_link_libraries(System.Native pthread)
endif ()

install_library_and_symbols (System.Native)
install (TARGETS System.Native-Static DESTINATION .)
