cmake_minimum_required(VERSION 3.10)

PROJECT("liboncrpc")

SET(VERSION_EXTERNAL "0.0.2")
SET(VERSION_PACKAGE  "1")
SET(PROJECT_VERSION  "${VERSION_EXTERNAL}.${VERSION_PACKAGE}")

#-----------------------------------------------------------------------------
#
# Build the library.
#
set(oncrpc_SOURCES
	auth_none.c
	auth_unix.c
	authunix_prot.c
	bcopy.c
	bindresv.c
	clnt_generic.c
	clnt_perror.c
	clnt_raw.c
	clnt_simple.c
	clnt_tcp.c
	clnt_udp.c
	get_myaddress.c
	getrpcent.c
	getrpcport.c
	nt.c
	pmap_clnt.c
	pmap_getport.c
	pmap_getmaps.c
	pmap_prot.c
	pmap_prot2.c
	pmap_rmt.c
	rpc_callmsg.c
	rpc_commondata.c
	rpc_prot.c
	svc_auth_nodes.c
	svc_auth_unix.c
	svc.c
	svc_raw.c
	svc_run.c
	svc_simple.c
	svc_tcp.c
	svc_udp.c
	xdr_array.c
	xdr.c
	xdr_float.c
	xdr_mem.c
	xdr_rec.c
	xdr_reference.c
	xdr_stdio.c
)

SET(oncrpc_PUBLIC_HEADERS
	rpc/auth.h
	rpc/auth_unix.h
	rpc/clnt.h
	rpc/netdb.h
	rpc/rpc.h
	rpc/rpc_msg.h
	rpc/svc_auth.h
	rpc/svc.h
	rpc/types.h
	rpc/xdr.h
)

# Build the library.
ADD_LIBRARY(TARGET_oncrpc STATIC ${oncrpc_SOURCES})
TARGET_INCLUDE_DIRECTORIES(TARGET_oncrpc PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
TARGET_LINK_LIBRARIES(TARGET_oncrpc PUBLIC -Wl,-Bstatic pthread ws2_32 iphlpapi)

SET_TARGET_PROPERTIES(TARGET_oncrpc PROPERTIES OUTPUT_NAME "oncrpc")

# This static library will be linked with a shared object later. Add "-fPIC" on GCC systems or X64 builds break.
IF("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU"  AND  "${CMAKE_SYSTEM_NAME}" STREQUAL "Linux")
	SET_TARGET_PROPERTIES(TARGET_oncrpc PROPERTIES COMPILE_FLAGS -fPIC)
ENDIF("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU"  AND  "${CMAKE_SYSTEM_NAME}" STREQUAL "Linux")


#-----------------------------------------------------------------------------
#
# Install all components of the library.
# This is the library itself and the public headers.
#
SET(CMAKE_INSTALL_PREFIX /install)
INSTALL(TARGETS TARGET_oncrpc
        EXPORT EXPORT_package
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include)
INSTALL(FILES ${oncrpc_PUBLIC_HEADERS}
        DESTINATION include/rpc)


#----------------------------------------------------------------------------
#
# Build the CMAKE package.
#
INCLUDE(CMakePackageConfigHelpers)

SET(INCLUDE_INSTALL_DIR include/)
CONFIGURE_PACKAGE_CONFIG_FILE(
        installer/config.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/package/${PROJECT_NAME}-config.cmake"
        INSTALL_DESTINATION cmake
        PATH_VARS INCLUDE_INSTALL_DIR
)

WRITE_BASIC_PACKAGE_VERSION_FILE(
        "${CMAKE_CURRENT_BINARY_DIR}/package/${PROJECT_NAME}-version.cmake"
        COMPATIBILITY ExactVersion
)

EXPORT(
        EXPORT EXPORT_package
        FILE "${CMAKE_CURRENT_BINARY_DIR}/package/${PROJECT_NAME}-targets.cmake"
        NAMESPACE ${PROJECT_NAME}::
)

INSTALL(
        EXPORT EXPORT_package
        FILE ${PROJECT_NAME}-targets.cmake
        NAMESPACE ${PROJECT_NAME}::
        DESTINATION cmake
)
INSTALL(
        FILES "${CMAKE_CURRENT_BINARY_DIR}/package/${PROJECT_NAME}-config.cmake" "${CMAKE_CURRENT_BINARY_DIR}/package/${PROJECT_NAME}-version.cmake"
        DESTINATION cmake
        COMPONENT Devel
)


#----------------------------------------------------------------------------
#
# Build a package for the selected platform.
#
SET(CPACK_OUTPUT_FILE_PREFIX ${CMAKE_BINARY_DIR}/../repository/net/sourceforge/oncrpc/oncrpc/${PROJECT_VERSION})
SET(CPACK_INCLUDE_TOPLEVEL_DIRECTORY FALSE)
SET(CPACK_PACKAGE_FILE_NAME "oncrpc-${PROJECT_VERSION}-${JONCHKI_PLATFORM}")

SET(CPACK_GENERATOR "TXZ")
INCLUDE(CPack)

IF(${JONCHKI_PLATFORM_DIST_ID} STREQUAL "windows")
	# Package a windows build.

	CONFIGURE_FILE(installer/install_windows.lua
	               ${CMAKE_CURRENT_BINARY_DIR}/package/install.lua
	               @ONLY)
ENDIF(${JONCHKI_PLATFORM_DIST_ID} STREQUAL "windows")

INSTALL(FILES ${CMAKE_CURRENT_BINARY_DIR}/package/install.lua
        DESTINATION .)

INSTALL(FILES ${oncrpc_PUBLIC_HEADERS}
        DESTINATION include/rpc)
INSTALL(FILES $<TARGET_FILE:TARGET_oncrpc>
        DESTINATION lib)

CONFIGURE_FILE(installer/oncrpc.xml
               ${CPACK_OUTPUT_FILE_PREFIX}/oncrpc-${PROJECT_VERSION}-${JONCHKI_PLATFORM}.xml
               @ONLY)

#-----------------------------------------------------------------------------
#
# Create a custom target named "pack".
# It builds the package and creates the hashes.
#
ADD_CUSTOM_TARGET(pack
                  COMMAND ${CMAKE_MAKE_PROGRAM}
                  COMMAND ${CMAKE_MAKE_PROGRAM} package
                  COMMAND "${PYTHON_EXECUTABLE}" ${CMAKE_HOME_DIRECTORY}/cmake/tools/generate_hash.py ${CPACK_OUTPUT_FILE_PREFIX}/oncrpc-${PROJECT_VERSION}-${JONCHKI_PLATFORM}.xml
                  COMMAND "${PYTHON_EXECUTABLE}" ${CMAKE_HOME_DIRECTORY}/cmake/tools/generate_hash.py ${CPACK_OUTPUT_FILE_PREFIX}/oncrpc-${PROJECT_VERSION}-${JONCHKI_PLATFORM}.tar.xz
                  COMMENT "Pack the artifact and generate the hashes...")


#-----------------------------------------------------------------------------
#
# Filter the pom.xml file.
#
SET(POM_ARTIFACT_ID  "oncrpc")
SET(POM_GROUP_ID     "net.sourceforge.oncrpc")
SET(POM_VERSION      "${PROJECT_VERSION}")
SET(POM_PACKAGING    "tar.xz")
CONFIGURE_FILE(installer/pom.xml
               ${CPACK_OUTPUT_FILE_PREFIX}/oncrpc-${PROJECT_VERSION}.pom
               @ONLY)
