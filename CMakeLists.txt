cmake_minimum_required(VERSION 3.11.1)
cmake_policy(VERSION 3.11.1)

project(
  geGL
  VERSION 1.0.3
  )

SET(CMAKE_CXX_STANDARD 14)

include(GenerateExportHeader)
include(GNUInstallDirs)

option(BUILD_SHARED_LIBS "build this library as shared")

SET(CMAKE_DEBUG_POSTFIX "d" CACHE STRING "add a postfix, usually d on windows")
SET(CMAKE_RELEASE_POSTFIX "" CACHE STRING "add a postfix, usually empty on windows")

set(HEADER_PATH ${CMAKE_CURRENT_LIST_DIR}/src/${PROJECT_NAME})
set(SRC_PATH ${CMAKE_CURRENT_LIST_DIR}/src/${PROJECT_NAME})

set(SOURCES
  ${SRC_PATH}/Buffer.cpp
  ${SRC_PATH}/VertexArray.cpp
  ${SRC_PATH}/Framebuffer.cpp
  ${SRC_PATH}/Texture.cpp
  ${SRC_PATH}/Sampler.cpp
  ${SRC_PATH}/ProgramPipeline.cpp
  ${SRC_PATH}/Shader.cpp
  ${SRC_PATH}/Program.cpp
  ${SRC_PATH}/Renderbuffer.cpp
  ${SRC_PATH}/AsynchronousQuery.cpp
  ${SRC_PATH}/DebugMessage.cpp
  ${SRC_PATH}/OpenGLObject.cpp
  ${SRC_PATH}/geGL.cpp
  ${SRC_PATH}/OpenGLContext.cpp
  ${SRC_PATH}/OpenGL.cpp
  ${SRC_PATH}/OpenGLUtil.cpp
  ${SRC_PATH}/StaticCalls.cpp
  ${SRC_PATH}/GLSLNoise.cpp
  )

set(INCLUDES
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}/Export.h
  ${HEADER_PATH}/Fwd.h
  ${HEADER_PATH}/Definitions.h
  ${HEADER_PATH}/Buffer.h
  ${HEADER_PATH}/VertexArray.h
  ${HEADER_PATH}/Framebuffer.h
  ${HEADER_PATH}/Texture.h
  ${HEADER_PATH}/Sampler.h
  ${HEADER_PATH}/ProgramPipeline.h
  ${HEADER_PATH}/Shader.h
  ${HEADER_PATH}/Program.h
  ${HEADER_PATH}/ProgramInfo.h
  ${HEADER_PATH}/Renderbuffer.h
  ${HEADER_PATH}/OpenGL.h
  ${HEADER_PATH}/OpenGLUtil.h
  ${HEADER_PATH}/AsynchronousQuery.h
  ${HEADER_PATH}/DebugMessage.h
  ${HEADER_PATH}/OpenGLObject.h
  ${HEADER_PATH}/geGL.h
  ${HEADER_PATH}/OpenGLFunctionTable.h
  ${HEADER_PATH}/OpenGLContext.h
  ${HEADER_PATH}/OpenGLCapabilities.h
  ${HEADER_PATH}/FunctionLoaderInterface.h
  ${HEADER_PATH}/DefaultLoader.h
  ${HEADER_PATH}/LoaderTableDecorator.h
  ${HEADER_PATH}/DSATableDecorator.h
  ${HEADER_PATH}/TrapTableDecorator.h
  ${HEADER_PATH}/CapabilitiesTableDecorator.h
  ${HEADER_PATH}/StaticCalls.h
  ${HEADER_PATH}/GLSLNoise.h
  )

set(GENERATED_INCLUDES
  ${HEADER_PATH}/Generated/ContextCalls.h
  ${HEADER_PATH}/Generated/ContextDeclarations.h
  ${HEADER_PATH}/Generated/FunctionTableCalls.h
  ${HEADER_PATH}/Generated/LoaderImplementation.h
  ${HEADER_PATH}/Generated/LoaderLoadingCalls.h
  ${HEADER_PATH}/Generated/NumberOfFunctions.h
  ${HEADER_PATH}/Generated/MemberOpenGLFunctions.h
  ${HEADER_PATH}/Generated/MemberOpenGLPFN.h
  ${HEADER_PATH}/Generated/OpenGLConstants.h
  ${HEADER_PATH}/Generated/OpenGLFunctions.h
  ${HEADER_PATH}/Generated/OpenGLPFN.h
  ${HEADER_PATH}/Generated/OpenGLTypes.h
  ${HEADER_PATH}/Generated/TrapCalls.h
  ${HEADER_PATH}/Generated/TrapImplementation.h
  )


add_library(${PROJECT_NAME} ${SOURCES} ${INCLUDES} ${GENERATED_INCLUDES})
add_library(ge::gl ALIAS ${PROJECT_NAME})

GENERATE_EXPORT_HEADER(${PROJECT_NAME}
   EXPORT_FILE_NAME "${PROJECT_NAME}/Export.h"
)

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/src>
    $<BUILD_INTERFACE:${CMAKE_BINARY_DIR}/${PROJECT_NAME}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE ON)

set(PROJECT_NAME_LOWER)
string(TOLOWER ${PROJECT_NAME} PROJECT_NAME_LOWER)


set_property(TARGET ${PROJECT_NAME} PROPERTY VERSION ${PROJECT_VERSION})
set_property(TARGET ${PROJECT_NAME} PROPERTY SOVERSION ${PROJECT_VERSION_MAJOR})
set_property(TARGET ${PROJECT_NAME} PROPERTY INTERFACE_${PROJECT_NAME}_MAJOR_VERSION ${PROJECT_VERSION_MAJOR})
set_property(TARGET ${PROJECT_NAME} APPEND PROPERTY COMPATIBLE_INTERFACE_STRING ${PROJECT_NAME}_MAJOR_VERSION)

#########################################
# Install

install(FILES
   ${RG_INCLUDES}
   DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}
)

install(FILES
  ${GENERATED_INCLUDES}
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}/Generated
)

install(TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}Targets
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
  )

file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake
  "include($" "{CMAKE_CURRENT_LIST_DIR}/${PROJECT_NAME}Targets.cmake)"
  )
  
#########################################
#Export

set(ConfigPackageLocation ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})

export(EXPORT ${PROJECT_NAME}Targets
  FILE ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake
  #NAMESPACE ${PROJECT_NAME}::
  )

install(EXPORT ${PROJECT_NAME}Targets
  DESTINATION ${ConfigPackageLocation}
)


install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake
  DESTINATION  ${ConfigPackageLocation}
  )

