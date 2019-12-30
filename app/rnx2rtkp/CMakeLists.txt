add_executable(rnx2rtkp "")
target_sources(rnx2rtkp PRIVATE rnx2rtkp.c)
target_link_libraries(rnx2rtkp rtk)