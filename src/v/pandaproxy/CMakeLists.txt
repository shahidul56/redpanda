add_subdirectory(client)

seastar_generate_swagger(
  TARGET admin_config_swagger
  VAR admin_config_swagger_file
  IN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/admin/api-doc/config.json
  OUT_FILE ${CMAKE_CURRENT_BINARY_DIR}/admin/api-doc/config.json.h
)

seastar_generate_swagger(
  TARGET api_health_swagger
  VAR api_health_swagger_file
  IN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/api/api-doc/health.json
  OUT_FILE ${CMAKE_CURRENT_BINARY_DIR}/api/api-doc/health.json.h
)

seastar_generate_swagger(
  TARGET get_topics_names_swagger
  VAR get_topics_names_swagger_file
  IN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/api/api-doc/get_topics_names.json
  OUT_FILE ${CMAKE_CURRENT_BINARY_DIR}/api/api-doc/get_topics_names.json.h
)

seastar_generate_swagger(
  TARGET post_topics_name_swagger
  VAR post_topics_name_swagger_file
  IN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/api/api-doc/post_topics_name.json
  OUT_FILE ${CMAKE_CURRENT_BINARY_DIR}/api/api-doc/post_topics_name.json.h
)

find_package(Base64 REQUIRED)

v_cc_library(
  NAME rest_application
  SRCS
    application.cc
    configuration.cc
    handlers.cc
    logger.cc
    probe.cc
    proxy.cc
    server.cc
    $<TARGET_OBJECTS:Base64::base64>
  DEPS
    v::pandaproxy_client
    v::syschecks
    v::kafka
    v::ssx
  )

add_executable(pandaproxy
  main.cc
)
target_link_libraries(pandaproxy PUBLIC v::rest_application)
set_property(TARGET pandaproxy PROPERTY POSITION_INDEPENDENT_CODE ON)
add_dependencies(v_rest_application
  admin_config_swagger
  api_health_swagger
  get_topics_names_swagger
  post_topics_name_swagger
)

if(CMAKE_BUILD_TYPE MATCHES Release)
  include(CheckIPOSupported)
  check_ipo_supported(RESULT ltosupported OUTPUT error)
  if(ltosupported)
    set_property(TARGET pandaproxy PROPERTY INTERPROCEDURAL_OPTIMIZATION ON)
  endif()
endif()
install(TARGETS pandaproxy DESTINATION bin)

add_subdirectory(json)
add_subdirectory(test)
