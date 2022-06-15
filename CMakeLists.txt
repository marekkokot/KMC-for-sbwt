cmake_minimum_required(VERSION 3.1)
project(KMC VERSION 1.0
  DESCRIPTION "SBWT fork of KMC"
  LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(KMC_MAIN_DIR "${CMAKE_CURRENT_SOURCE_DIR}/kmc_core")
set(KMC_CLI_DIR "${CMAKE_CURRENT_SOURCE_DIR}/kmc_CLI")
set(KMC_API_DIR "${CMAKE_CURRENT_SOURCE_DIR}/kmc_api")
set(KMC_DUMP_DIR "${CMAKE_CURRENT_SOURCE_DIR}/kmc_dump")
set(KMC_TOOLS_DIR "${CMAKE_CURRENT_SOURCE_DIR}/kmc_tools")
set(PY_KMC_API_DIR "${CMAKE_CURRENT_SOURCE_DIR}/py_kmc_api")

if(APPLE)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -m64 -static-libgcc -static-libstdc++ -pthread -std=c++14")
  set(LINK_OPTIONS "-lm -static-libgcc -static-libstdc++ -O3 -pthread -std=c++14")
  set(STATIC_LIBRARY_OPTIONS "-lm -static-libgcc -static-libstdc++ -O3 -pthread -std=c++14")
  set(RADULS_OBJS "")
  set(KMC_TOOLS_LIBS
    "${KMC_TOOLS_DIR}/libs/libz.1.2.5.dylib;${KMC_TOOLS_DIR}/libs/libbz2.1.0.5.dylib")

else()
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -m64 -static -Wl,--whole-archive -lpthread -Wl,--no-whole-archive -std=c++14")
  set(LINK_OPTIONS "-lm -static -O3 -Wl,--whole-archive -lpthread -Wl,--no-whole-archive -std=c++14")
  set(STATIC_LIBRARY_OPTIONS "-lm -static -O3 -Wl,--whole-archive -lpthread -Wl,--no-whole-archive -std=c++14")
  set(RADULS_OBJS
    "${KMC_MAIN_DIR}/raduls_sse2.cpp;${KMC_MAIN_DIR}/raduls_sse41.cpp;${KMC_MAIN_DIR}/raduls_avx2.cpp;${KMC_MAIN_DIR}/raduls_avx.cpp")
  set(KMC_TOOLS_LIBS
    "${KMC_TOOLS_DIR}/libs/libz.a;${KMC_TOOLS_DIR}/libs/libbz2.a")
endif()

set(KMC_CLI_OBJS "${KMC_CLI_DIR}/kmc.cpp")
set(KFF_OBJS "${KMC_MAIN_DIR}/kff_writer.cpp")

set(KMC_CORE_OBJS
  "${KMC_MAIN_DIR}/mem_disk_file.cpp;${KMC_MAIN_DIR}/rev_byte.cpp;${KMC_MAIN_DIR}/bkb_writer.cpp;${KMC_MAIN_DIR}/cpu_info.cpp;${KMC_MAIN_DIR}/bkb_reader.cpp;${KMC_MAIN_DIR}/fastq_reader.cpp;${KMC_MAIN_DIR}/timer.cpp;${KMC_MAIN_DIR}/develop.cpp;${KMC_MAIN_DIR}/kb_completer.cpp;${KMC_MAIN_DIR}/kb_storer.cpp;${KMC_MAIN_DIR}/kmer.cpp;${KMC_MAIN_DIR}/splitter.cpp;${KMC_MAIN_DIR}/kb_collector.cpp;${KMC_MAIN_DIR}/kmc_runner.cpp")

set(KMC_TOOLS_OBJS
 "${KMC_TOOLS_DIR}/kmer_file_header.cpp;${KMC_TOOLS_DIR}/kmc_tools.cpp;${KMC_TOOLS_DIR}/nc_utils.cpp;${KMC_TOOLS_DIR}/parameters_parser.cpp;${KMC_TOOLS_DIR}/parser.cpp;${KMC_TOOLS_DIR}/tokenizer.cpp;${KMC_TOOLS_DIR}/fastq_filter.cpp;${KMC_TOOLS_DIR}/fastq_reader.cpp;${KMC_TOOLS_DIR}/fastq_writer.cpp;${KMC_TOOLS_DIR}/percent_progress.cpp;${KMC_TOOLS_DIR}/kff_info_reader.cpp;${KMC_API_DIR}/mmer.cpp;${KMC_API_DIR}/kmc_file.cpp;${KMC_API_DIR}/kmer_api.cpp")

add_library(kmc_core STATIC ${KMC_CORE_OBJS} ${RADULS_OBJS} ${KMC_API_OBJS} ${KFF_OBJS})

add_library(kmc_tools STATIC ${KMC_TOOLS_OBJS} ${KMC_API_OBJS} ${KFF_OBJS} ${KMC_TOOLS_LIBS})
# target_link_libraries(kmc_tools PUBLIC ${KMC_TOOLS_LIBS})

target_include_directories(kmc_core PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_include_directories(kmc_tools PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
