add_library(predict SHARED orbit.c observer.c sdp4.c sgp4.c unsorted.c)

add_library(predict_static STATIC orbit.c observer.c sdp4.c sgp4.c unsorted.c)
set_target_properties(predict_static PROPERTIES OUTPUT_NAME predict)

# The VERSION is the version number in the file name:
# libpredict.so.<VERSION>. This should be the full major.minor.patch
# version number.
set_target_properties(predict PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(predict_static PROPERTIES VERSION ${PROJECT_VERSION})

# The SOVERSION is the version number in the soname:
# libpredict.so.<SOVERSION>. This should be only the major version
# number, in order for applications to get a runtime dependency on an
# ABI version compatible with the library at build time.
set_target_properties(predict PROPERTIES SOVERSION ${PROJECT_VERSION_MAJOR})
set_target_properties(predict_static PROPERTIES SOVERSION ${PROJECT_VERSION_MAJOR})

# Header files needed to compile an application using libpredict
set(predict_public_header)
list(APPEND predict_public_header
  ${LIBPREDICT_INCLUDE_DIR}/predict/orbit.h
  ${LIBPREDICT_INCLUDE_DIR}/predict/observer.h
  ${LIBPREDICT_INCLUDE_DIR}/predict/tle.h
)
set_target_properties(predict PROPERTIES PUBLIC_HEADER "${predict_public_header}")
#target_include_directories(predict PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
# Maybe needed on Mac OS?
#set_target_properties(predict PROPERTIES FRAMEWORK TRUE)

# Tell the linker to use the symbol version script
get_property(predict_link_flags TARGET predict PROPERTY LINK_FLAGS)
set(predict_link_flags "${predict_link_flags} -Wl,--version-script=${CMAKE_CURRENT_SOURCE_DIR}/libpredict.symver")
set_target_properties(predict PROPERTIES LINK_FLAGS ${predict_link_flags})

install(TARGETS predict predict_static
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  PUBLIC_HEADER DESTINATION include/predict
)

# pkg-config file
configure_file(predict.pc.in predict.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/predict.pc DESTINATION share/pkgconfig)
