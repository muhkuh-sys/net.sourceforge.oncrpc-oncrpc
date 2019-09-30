cmake_minimum_required(VERSION 3.10)

PROJECT("liboncrpc"
        VERSION 0.0.2.1)

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


# Build the library.
ADD_LIBRARY(TARGET_oncrpc STATIC ${oncrpc_SOURCES})
TARGET_INCLUDE_DIRECTORIES(TARGET_oncrpc PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
#INCLUDE_DIRECTORIES(TARGET_mhash PUBLIC include lib)

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
#SET(CMAKE_INSTALL_PREFIX /install)
#INSTALL(TARGETS TARGET_oncrpc
#        EXPORT EXPORT_package
#        LIBRARY DESTINATION lib
#        ARCHIVE DESTINATION lib
#        RUNTIME DESTINATION bin
#        INCLUDES DESTINATION include)
#INSTALL(FILES include/mhash.h
#        DESTINATION include)
#INSTALL(FILES include/mutils/mglobal.h include/mutils/mhash.h include/mutils/mhash_config.h include/mutils/mincludes.h include/mutils/mtypes.h include/mutils/mutils.h
#        DESTINATION include/mutils)


#	rpc/auth.h
#	rpc/auth_unix.h
#bcopy.h
#	rpc/clnt.h
#	rpc/netdb.h
#pmap_cln.h
#pmap_pro.h
#pmap_rmt.h
#	rpc/rpc.h
#	rpc/rpc_msg.h
#	rpc/svc_auth.h
#	rpc/svc.h
#	rpc/types.h
#	rpc/xdr.h

