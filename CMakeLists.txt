cmake_minimum_required(VERSION 3.2 FATAL_ERROR)
#-----------------------------------------------------------------
# The project
#-----------------------------------------------------------------
project (GPerfTools VERSION 2.6)


if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "release")
endif()

string(TOLOWER "${CMAKE_BUILD_TYPE}" CMAKE_BUILD_TYPE)

#force a single configuration type on Windows builds
set(CMAKE_CONFIGURATION_TYPES "${CMAKE_BUILD_TYPE}" CACHE STRING "" FORCE)

install(
  FILES
    "${CMAKE_CURRENT_BINARY_DIR}/GPerfToolsConfigVersion.cmake"
  DESTINATION
    lib/cmake/GPerfTools
  COMPONENT
    Devel
)

include_directories("${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/src" "${CMAKE_SOURCE_DIR}/src/base" "${CMAKE_SOURCE_DIR}/src/windows" "${CMAKE_BINARY_DIR}" )

# libtcmalloc_minimal
set(TCMallocMinimalHeaders
    src/addressmap-inl.h
    src/base/atomicops.h
    src/base/atomicops-internals-linuxppc.h
    src/base/atomicops-internals-macosx.h
    src/base/atomicops-internals-windows.h
    src/base/atomicops-internals-x86.h
    src/base/basictypes.h
    src/base/commandlineflags.h
    src/base/googleinit.h
    src/base/logging.h
    src/base/low_level_alloc.h
    src/base/spinlock.h
    src/base/spinlock_internal.h
    src/base/spinlock_linux-inl.h
    src/base/spinlock_posix-inl.h
    src/base/spinlock_win32-inl.h
    src/base/stl_allocator.h
    src/base/sysinfo.h
    src/base/thread_annotations.h
    src/central_freelist.h
    src/common.h
    src/gperftools/heap-checker.h
    src/gperftools/heap-profiler.h
    src/gperftools/profiler.h
    src/gperftools/stacktrace.h
    src/heap-profile-table.h
    src/internal_logging.h
    src/malloc_hook-inl.h
    src/memory_region_map.h
    src/packed-cache-inl.h
    src/page_heap.h
    src/page_heap_allocator.h
    src/pagemap.h
    src/raw_printer.h
    src/sampler.h
    src/span.h
    src/stack_trace_table.h
    src/stacktrace_win32-inl.h
    src/static_vars.h
    src/symbolize.h
    src/system-alloc.h
    src/tcmalloc.h
    src/thread_cache.h
)

set(TCMallocMinimalGperfHeaders
    src/gperftools/malloc_extension.h
    src/gperftools/malloc_extension_c.h
    src/gperftools/malloc_hook.h
    src/gperftools/malloc_hook_c.h
    src/gperftools/nallocx.h
)

if(WIN32)
    list(APPEND TCMallocMinimalHeaders
        src/windows/config.h
        src/windows/mini_disassembler.h
        src/windows/mini_disassembler_types.h
        src/windows/port.h
        src/windows/preamble_patcher.h
    )
    list(APPEND TCMallocMinimalGperfHeaders src/windows/gperftools/tcmalloc.h)
endif(WIN32)


set(TCMallocMinimalSources
    src/base/dynamic_annotations.c
    src/base/logging.cc
    src/base/low_level_alloc.cc
    src/base/spinlock.cc
    src/base/spinlock_internal.cc
    src/base/sysinfo.cc
    src/central_freelist.cc
    src/common.cc
    src/fake_stacktrace_scope.cc
    src/heap-profile-table.cc
    src/internal_logging.cc
    src/malloc_extension.cc
    src/malloc_hook.cc
    src/memory_region_map.cc
    src/page_heap.cc
    src/raw_printer.cc
    src/sampler.cc
    src/span.cc
    src/stack_trace_table.cc
    src/stacktrace.cc
    src/static_vars.cc
    src/symbolize.cc
    src/thread_cache.cc
)

if(WIN32)
    list(APPEND TCMallocMinimalSources
        src/windows/ia32_modrm_map.cc
        src/windows/ia32_opcode_map.cc
        src/windows/mini_disassembler.cc
        src/windows/patch_functions.cc
        src/windows/port.cc
        src/windows/preamble_patcher.cc
        src/windows/preamble_patcher_with_stub.cc
        src/windows/system-alloc.cc
    )
endif(WIN32)

add_library(libtcmalloc_minimal SHARED ${TCMallocMinimalHeaders} ${TCMallocMinimalGperfHeaders} ${TCMallocMinimalSources})
add_library(libtcmalloc_minimal_static STATIC ${TCMallocMinimalHeaders} ${TCMallocMinimalGperfHeaders} ${TCMallocMinimalSources})
#set_target_properties(libtcmalloc_minimal_static PROPERTIES COMPILE_FLAGS "/D PERFTOOLS_DLL_DECL=")
#target_compile_definitions(libtcmalloc_minimal PRIVATE "LIBTCMALLOC_MINIMAL_EXPORTS")

install (FILES ${TCMallocMinimalGperfHeaders} DESTINATION include/gperftools)
install (TARGETS libtcmalloc_minimal DESTINATION lib EXPORT GPerfToolsConfig)
install (TARGETS libtcmalloc_minimal_static DESTINATION lib EXPORT GPerfToolsConfig)

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/GPerfToolsConfigVersion.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion
)

export(EXPORT GPerfToolsConfig FILE GPerfToolsConfig.cmake)

install(EXPORT GPerfToolsConfig DESTINATION lib/cmake/GPerfTools)