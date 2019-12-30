add_executable(pos2kml "")
target_sources(pos2kml PRIVATE pos2kml.c)
target_link_libraries(pos2kml PRIVATE rtk)