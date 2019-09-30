cmake_minimum_required(VERSION 3.10)

PROJECT("liboncrpc"
        VERSION 0.0.2.1)

#-----------------------------------------------------------------------------
#
# Build the library.
#
set(oncrpc_SOURCES
	Auth_non.c
	Auth_uni.c
	authunix.c
	bcopy.c
	Bindresv.c
	clnt_gen.c
	Clnt_per.c
	clnt_raw.c
	clnt_sim.c
	Clnt_tcp.c
	Clnt_udp.c
	Get_myad.c
	Getrpcen.c
	Getrpcpo.c
	nt.c
	Pmap_cln.c
	pmap_get.c
	pmap_gma.c
	pmap_pr.c
	pmap_pro.c
	Pmap_rmt.c
	rpc_call.c
	rpc_comm.c
	rpc_prot.c
	svc_auth.c
	Svc_autu.c
	Svc.c
	svc_raw.c
	Svc_run.c
	Svc_simp.c
	Svc_tcp.c
	Svc_udp.c
	Xdr_arra.c
	Xdr.c
	xdr_floa.c
	xdr_mem.c
	Xdr_rec.c
	Xdr_refe.c
	xdr_stdi.c
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
#INSTALL(TARGETS TARGET_mhash
#        EXPORT EXPORT_package
#        LIBRARY DESTINATION lib
#        ARCHIVE DESTINATION lib
#        RUNTIME DESTINATION bin
#        INCLUDES DESTINATION include)
#INSTALL(FILES include/mhash.h
#        DESTINATION include)
#INSTALL(FILES include/mutils/mglobal.h include/mutils/mhash.h include/mutils/mhash_config.h include/mutils/mincludes.h include/mutils/mtypes.h include/mutils/mutils.h
#        DESTINATION include/mutils)
