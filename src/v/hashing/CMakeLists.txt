find_package(xxHash REQUIRED)
find_package(Crc32c REQUIRED)
v_cc_library(
  NAME rphashing
  SRCS murmur.cc
  COPTS
    -Wno-implicit-fallthrough
  DEPS
    xxHash::xxhash
    Crc32c::crc32c
  DEFINES
    -DXXH_PRIVATE_API
)

rp_test(
  UNIT_TEST
  BINARY_NAME xx_hashes
  SOURCES xx_tests.cc
  DEFINITIONS BOOST_TEST_DYN_LINK
  LIBRARIES Boost::unit_test_framework v::rphashing
  LABELS hashing
)
rp_test(
  BENCHMARK_TEST
  BINARY_NAME hashing_bench
  SOURCES hash_bench.cc
  LIBRARIES Seastar::seastar_perf_testing v::rphashing
  LABELS hashing
)
