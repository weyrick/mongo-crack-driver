
cmake_minimum_required(VERSION 2.8)
project (mongo-crack)

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/modules )

MESSAGE( STATUS "PROJECT_SOURCE_DIR: " ${PROJECT_SOURCE_DIR} )
MESSAGE( STATUS "CMAKE_MODULE_PATH: " ${CMAKE_MODULE_PATH} )
MESSAGE( STATUS "CMAKE_SYSTEM_PROCESSOR: " ${CMAKE_SYSTEM_PROCESSOR} )

# XXX find crack
SET(CRACK_INCLUDE_DIR "/home/weyrick/workspace/crack-language/")
SET(CRACK_BIN "/home/weyrick/bin/crack")

SET(BINDING_CRK_SRC ${PROJECT_SOURCE_DIR}/bindings/mongo-c-bindings.crk)
SET(BINDING_EXT_SRC ${CMAKE_CURRENT_BINARY_DIR}/mongo-c-bindings.cpp)

ADD_CUSTOM_COMMAND(
   COMMAND ${CRACK_BIN}
   ARGS ${BINDING_CRK_SRC}
   DEPENDS ${BINDING_CRK_SRC}
   OUTPUT ${BINDING_EXT_SRC})
SET_SOURCE_FILES_PROPERTIES(${BINDING_EXT_SRC} GENERATED)

# XXX find mongoc
SET(MONGO_INCLUDE_DIR "/home/weyrick/workspace/mongo-c-driver/src/")
SET(MONGO_LIB_DIR "/home/weyrick/workspace/mongo-c-driver/")
ADD_DEFINITIONS("-DMONGO_HAVE_STDINT")

include_directories(${PROJECT_SOURCE_DIR}/bindings
                    ${CRACK_INCLUDE_DIR}
		    ${MONGO_INCLUDE_DIR}
		)

add_library(mongo-ext SHARED ${BINDING_EXT_SRC})
target_link_libraries(mongo-ext mongoc)
set_target_properties(mongo-ext
                      PROPERTIES
                      OUTPUT_NAME _mongoc
                      LINK_FLAGS "-L${MONGO_LIB_DIR}"
                      PREFIX ""
                      COMPILE_DEFINITIONS_DEBUG _DEBUG
                      )
