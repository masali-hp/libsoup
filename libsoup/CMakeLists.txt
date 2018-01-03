## This CMakeLists.txt build recipe was created to build on Windows;
## it has not been tested on non-Windows platforms.  It is likely missing
## some of the features present in the automake version (documentation, etc.).

if (WIN32)
    set(SOUP_LIBSLIBS
        ws2_32
    )
endif ()

set(SOUP_DEFINES -DG_LOG_DOMAIN="libsoup" -DLIBSOUP_COMPILATION -DHAVE_CONFIG_H -DDLL_EXPORT)

set(SOUP_HEADERS
    soup.h
    soup-address.h
    soup-auth.h
    soup-auth-domain.h
    soup-auth-domain-basic.h
    soup-auth-domain-digest.h
    soup-auth-manager.h
    soup-autocleanups.h
    soup-cache.h
    soup-content-decoder.h
    soup-content-sniffer.h
    soup-cookie.h
    soup-cookie-jar.h
    soup-cookie-jar-db.h
    soup-cookie-jar-text.h
    soup-date.h
    soup-form.h
    soup-headers.h
    soup-logger.h
    soup-message.h
    soup-message-body.h
    soup-message-headers.h
    soup-method.h
    soup-misc.h
    soup-multipart.h
    soup-multipart-input-stream.h
    soup-password-manager.h
    soup-portability.h
    soup-proxy-resolver.h
    soup-proxy-resolver-default.h
    soup-proxy-uri-resolver.h
    soup-request.h
    soup-request-data.h
    soup-request-file.h
    soup-request-http.h
    soup-requester.h
    soup-server.h
    soup-session.h
    soup-session-async.h
    soup-session-feature.h
    soup-session-sync.h
    soup-socket.h
    soup-status.h
    soup-tld.h
    soup-types.h
    soup-uri.h
    soup-value-utils.h
    soup-websocket.h
    soup-websocket-connection.h
    soup-xmlrpc.h
    soup-xmlrpc-old.h
)

set(SOUP_SOURCES
    soup-address.c
    soup-auth.c
    soup-auth-basic.c
    soup-auth-digest.c
    soup-auth-ntlm.c
    soup-auth-negotiate.c
    soup-auth-domain.c
    soup-auth-domain-basic.c
    soup-auth-domain-digest.c
    soup-auth-manager.c
    soup-body-input-stream.c
    soup-body-output-stream.c
    soup-cache.c
    soup-cache-client-input-stream.c
    soup-cache-input-stream.c
    soup-client-input-stream.c
    soup-connection.c
    soup-connection-auth.c
    soup-content-decoder.c
    soup-content-processor.c
    soup-content-sniffer.c
    soup-content-sniffer-stream.c
    soup-converter-wrapper.c
    soup-cookie.c
    soup-cookie-jar.c
    soup-cookie-jar-db.c
    soup-cookie-jar-text.c
    soup-date.c
    soup-directory-input-stream.c
    soup-filter-input-stream.c
    soup-form.c
    soup-headers.c
    soup-init.c
    soup-io-stream.c
    soup-logger.c
    soup-message.c
    soup-message-body.c
    soup-message-client-io.c
    soup-message-headers.c
    soup-message-io.c
    soup-message-queue.c
    soup-message-server-io.c
    soup-method.c
    soup-misc.c
    soup-multipart.c
    soup-multipart-input-stream.c
    soup-password-manager.c
    soup-path-map.c
    soup-proxy-resolver.c
    soup-proxy-resolver-default.c
    soup-proxy-resolver-wrapper.c
    soup-proxy-uri-resolver.c
    soup-request.c
    soup-request-data.c
    soup-request-file.c
    soup-request-http.c
    soup-requester.c
    soup-server.c
    soup-session.c
    soup-session-async.c
    soup-session-feature.c
    soup-session-sync.c
    soup-socket.c
    soup-socket-properties.c
    soup-status.c
    soup-tld.c
    soup-uri.c
    soup-value-utils.c
    soup-version.c
    soup-websocket.c
    soup-websocket-connection.c
    soup-xmlrpc.c
    soup-xmlrpc-old.c
)

# Generate soup-version.h
file(READ ${CMAKE_CURRENT_SOURCE_DIR}/../configure.ac CONFIGURE_AC)
string(REGEX MATCHALL "\\[([0-9a-z\\.:/]*)\\]"
    CONFIGURE_AC_PACKAGE_INFO ${CONFIGURE_AC}
)

function(strip_bracket VAR)
    string(LENGTH ${${VAR}} TMP_LEN)
    math(EXPR TMP_LEN ${TMP_LEN}-2)
    string(SUBSTRING ${${VAR}} 1 ${TMP_LEN} TMP_SUB)
    set(${VAR} ${TMP_SUB} PARENT_SCOPE)
endfunction()

list(GET CONFIGURE_AC_PACKAGE_INFO 0 MAJOR_VERSION)
strip_bracket(MAJOR_VERSION)
list(GET CONFIGURE_AC_PACKAGE_INFO 1 MINOR_VERSION)
strip_bracket(MINOR_VERSION)
list(GET CONFIGURE_AC_PACKAGE_INFO 2 MICRO_VERSION)
strip_bracket(MICRO_VERSION)

set(SOUP_MAJOR_VERSION ${MAJOR_VERSION})
set(SOUP_MINOR_VERSION ${MINOR_VERSION})
set(SOUP_MICRO_VERSION ${MICRO_VERSION})

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/soup-version.h.in ${CMAKE_CURRENT_BINARY_DIR}/soup-version.h)

# Generate config.h
if (WIN32)
    set(PACKAGE "libsoup")
    set(PACKAGE_BUGREPORT "http://bugzilla.gnome.org/enter_bug.cgi?product=libsoup")
    set(PACKAGE_NAME "libsoup")
    set(PACKAGE_VERSION ${SOUP_MAJOR_VERSION}.${SOUP_MINOR_VERSION}.${SOUP_MICRO_VERSION})
    configure_file(${CMAKE_SOURCE_DIR}/config.h.win32.in ${CMAKE_CURRENT_BINARY_DIR}/config.h)
endif ()

# TLD rules
set(TLD_DATA_FILE ${CMAKE_SOURCE_DIR}/data/effective_tld_names.dat)
set(TLD_DATA_INC ${CMAKE_CURRENT_BINARY_DIR}/tld_data.inc)
add_custom_command(
    OUTPUT ${TLD_DATA_INC}
    MAIN_DEPENDENCY ${TLD_DATA_FILE}
    COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/tld-parser.py ${TLD_DATA_FILE} ${TLD_DATA_INC}
    VERBATIM)
set_source_files_properties(${TLD_DATA_INC} PROPERTIES GENERATED TRUE)
ADD_SOURCE_DEPENDENCIES(${TLD_DATA_FILE} ${TLD_DATA_INC})

MAKE_ENUMS(
    ${CMAKE_CURRENT_BINARY_DIR}/soup-enum-types.h
    ${CMAKE_CURRENT_BINARY_DIR}/soup-enum-types.c
    "${SOUP_HEADERS}"
    ${CMAKE_CURRENT_SOURCE_DIR}
)

set (SOUP_GENERATED_SOURCES
    ${CMAKE_CURRENT_BINARY_DIR}/soup-enum-types.h
    ${CMAKE_CURRENT_BINARY_DIR}/soup-version.h
    ${CMAKE_CURRENT_BINARY_DIR}/soup-enum-types.c
    ${CMAKE_CURRENT_BINARY_DIR}/config.h
    ${TLD_DATA_INC}
)

add_definitions(${SOUP_DEFINES})
include_directories(${SOUP_INCLUDE_PATHS})
add_library(libsoup SHARED ${SOUP_SOURCES} ${SOUP_GENERATED_SOURCES} ${SOUP_HEADERS})

target_link_libraries(libsoup ${SOUP_LIBRARIES})

if (NOT SKIP_INSTALL_ALL)
    message(STATUS "Install target is ${CMAKE_INSTALL_PREFIX}")

    if (NOT SKIP_INSTALL_HEADERS)
        install(FILES ${SOUP_HEADERS} DESTINATION "include/libsoup")
        install(FILES ${CMAKE_CURRENT_BINARY_DIR}/soup-enum-types.h DESTINATION "include/libsoup")
        install(FILES ${CMAKE_CURRENT_BINARY_DIR}/soup-version.h DESTINATION "include/libsoup")
    endif ()

    if(NOT DEFINED CMAKE_INSTALL_LIBDIR)
        set(CMAKE_INSTALL_LIBDIR lib)
    endif(NOT DEFINED CMAKE_INSTALL_LIBDIR)

    install(TARGETS libsoup
          RUNTIME DESTINATION bin
          LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
          ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
          FRAMEWORK DESTINATION ${CMAKE_INSTALL_LIBDIR})
endif ()