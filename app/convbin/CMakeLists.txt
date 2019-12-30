add_executable(convbin "")
target_sources(convbin PRIVATE convbin.c)
target_link_libraries(convbin PRIVATE rtk)