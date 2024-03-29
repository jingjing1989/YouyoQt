cmake_minimum_required(VERSION 3.4.1)
project(QCefWing)

set(CMAKE_CXX_STANDARD 11)
set(CXX_STANDARD_REQUIRED)

file(GLOB_RECURSE _SRC_FILES
  "*.cpp"
  "*.h"
)
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} PREFIX Source FILES ${_SRC_FILES})

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${QCEF_WIDGET_SDK_BIN_OUT})

if (OS_WINDOWS)
  file(GLOB_RECURSE _RES_FILES
    "*.ico"
    "*.rc"
  ) 
  source_group("Resources" ${_RES_FILES})
endif()

add_executable(${PROJECT_NAME} WIN32
	${_SRC_FILES}
	${_RES_FILES}
)

if (OS_WINDOWS)
  target_compile_definitions(${PROJECT_NAME} PRIVATE
    UNICODE
    _UNICODE
  )
endif()

target_link_libraries(${PROJECT_NAME} PRIVATE
  ${CEF_DLL_WRAPPER}
  ${CEF_LIB_FILE}
)
add_dependencies(${PROJECT_NAME} ${CEF_DLL_WRAPPER})
# Embed the manifest file into the target
if (MSVC)
  if (CMAKE_MAJOR_VERSION LESS 3)
		message(WARNING "CMake version 3.0 or newer is required use build variable TARGET_FILE")
  else()
    if (NOT "QCefWing" STREQUAL "${PROJECT_NAME}")
       add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
			  COMMAND ${CMAKE_COMMAND} -E copy_if_different ${CMAKE_CURRENT_SOURCE_DIR}/QCefWing.manifest ${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}.manifest)
    endif()
    
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
			COMMAND mt.exe -manifest \"${CMAKE_CURRENT_SOURCE_DIR}\\${PROJECT_NAME}.manifest\" -inputresource:\"$<TARGET_FILE:${PROJECT_NAME}>\" -outputresource:\"$<TARGET_FILE:${PROJECT_NAME}>\")
  endif()
endif(MSVC)
