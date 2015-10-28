
PROJECT(ocrPlugin)

IF(EXISTS ${CMAKE_SOURCE_DIR}/CMakeUser.txt)
	include(${CMAKE_SOURCE_DIR}/CMakeUser.txt)
ENDIF()

# include macros needed
include("../cmake/Utils.cmake")

######################################################### CHANGE THIS FOR NEW PLUGINS ###################################################
set(PLUGIN_VERSION 0.0.1)
set(PLUGIN_ID 9791acc7e9654ff7888a9e857290956d) #GUID without hyphens generated at http://www.guidgenerator.com/
######################################################### CHANGE THIS FOR NEW PLUGINS ###################################################

add_definitions(-DPLUGIN_VERSION="${PLUGIN_VERSION}")
add_definitions(-DPLUGIN_ID="${PLUGIN_ID}")


if (NOT BUILDING_MULTIPLE_PLUGINS)
  # prepare plugin
  NMC_PREPARE_PLUGIN()
  
  # find the Qt
  NMC_FINDQT()

  # OpenCV
  NMC_FIND_OPENCV()
endif()

include_directories (
	${QT_INCLUDES}
	${OpenCV_INCLUDE_DIRS}
	${CMAKE_CURRENT_BINARY_DIR}
	${NOMACS_INCLUDE_DIRECTORY}
)

file(GLOB PLUGIN_SOURCES "src/*.cpp")
file(GLOB PLUGIN_HEADERS "src/*.h" "${NOMACS_INCLUDE_DIRECTORY}/DkPluginInterface.h")
# file(GLOB PLUGIN_MOCS "src/*.h" "${NOMACS_INCLUDE_DIRECTORY}/DkPluginInterface.h")

set (PLUGIN_RESOURCES
        src/nomacsPlugin.qrc
)
ADD_DEFINITIONS(${QT_DEFINITIONS})
ADD_DEFINITIONS(-DQT_PLUGIN)
ADD_DEFINITIONS(-DQT_SHARED)
ADD_DEFINITIONS(-DQT_DLL)

QT5_ADD_RESOURCES(PLUGIN_RCC ${PLUGIN_RESOURCES})

link_directories(${OpenCV_LIBRARY_DIRS} ${NOMACS_BUILD_DIRECTORY}/$(CONFIGURATION) ${NOMACS_BUILD_DIRECTORY}/libs ${NOMACS_BUILD_DIRECTORY})
ADD_LIBRARY(${PROJECT_NAME} SHARED ${PLUGIN_SOURCES} ${PLUGIN_MOC_SRC} ${PLUGIN_RCC} ${PLUGIN_HEADERS})	
target_link_libraries(${PROJECT_NAME} ${QT_QTCORE_LIBRARY} ${QT_QTGUI_LIBRARY} ${QT_QTNETWORK_LIBRARY} ${QT_QTMAIN_LIBRARY} ${OpenCV_LIBS} ${NOMACS_LIBS})

NMC_CREATE_TARGETS()


qt5_use_modules(${PROJECT_NAME} Widgets Gui Network LinguistTools PrintSupport Concurrent)