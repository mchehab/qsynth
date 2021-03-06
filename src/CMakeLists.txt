# project(qsynth)

include_directories (
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_BINARY_DIR}
)

configure_file (cmake_config.h.in ${CMAKE_CURRENT_BINARY_DIR}/config.h)

set (HEADERS
  qsynth.h
  qsynthKnob.h
  qsynthMeter.h
  qsynthSystemTray.h
  qsynthTabBar.h
  qsynthAboutForm.h
  qsynthChannelsForm.h
  qsynthMainForm.h
  qsynthMessagesForm.h
  qsynthOptionsForm.h
  qsynthPaletteForm.h
  qsynthPresetForm.h
  qsynthSetupForm.h
)

set (SOURCES
  qsynth.cpp
  qsynthEngine.cpp
  qsynthChannels.cpp
  qsynthKnob.cpp
  qsynthMeter.cpp
  qsynthSetup.cpp
  qsynthOptions.cpp
  qsynthSystemTray.cpp
  qsynthTabBar.cpp
  qsynthAboutForm.cpp
  qsynthChannelsForm.cpp
  qsynthMainForm.cpp
  qsynthMessagesForm.cpp
  qsynthOptionsForm.cpp
  qsynthPaletteForm.cpp
  qsynthPresetForm.cpp
  qsynthSetupForm.cpp
  qsynthDialClassicStyle.cpp
  qsynthDialPeppinoStyle.cpp
  qsynthDialVokiStyle.cpp
  qsynthDialSkulptureStyle.cpp
)

set (FORMS
  qsynthAboutForm.ui
  qsynthChannelsForm.ui
  qsynthMainForm.ui
  qsynthMessagesForm.ui
  qsynthOptionsForm.ui
  qsynthPaletteForm.ui
  qsynthPresetForm.ui
  qsynthSetupForm.ui
)

set (RESOURCES
  qsynth.qrc
)

set ( TRANSLATIONS
   translations/qsynth_cs.ts
   translations/qsynth_de.ts
   translations/qsynth_es.ts
   translations/qsynth_fr.ts
   translations/qsynth_ru.ts
   translations/qsynth_sr.ts
)

qt_wrap_ui (UI_SOURCES ${FORMS})
qt_wrap_cpp (MOC_SOURCES ${HEADERS})
qt_add_resources (QRC_SOURCES ${RESOURCES})
qt_add_translation (QM_FILES ${TRANSLATIONS})

add_custom_target (translations ALL DEPENDS ${QM_FILES})

if (WIN32)
  set (RC_FILE ${CMAKE_CURRENT_SOURCE_DIR}/win32/${PROJECT_NAME}.rc)
  set (RES_FILE ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.res.obj)
  find_program (WINDRES_EXECUTABLE NAMES windres mingw32-windres i686-mingw32-windres)
  if (MINGW)
    exec_program (${WINDRES_EXECUTABLE}
      ARGS "-i ${RC_FILE} -o ${RES_FILE} --include-dir=${CMAKE_CURRENT_SOURCE_DIR}/images")
    list (APPEND SOURCES ${RES_FILE})
  else ()
    list (APPEND SOURCES ${RC_FILE})
  endif ()
endif ()

if (APPLE)
  set (ICON_FILE ${CMAKE_CURRENT_SOURCE_DIR}/images/${PROJECT_NAME}.icns)
  list (APPEND SOURCES ${ICON_FILE})
  set (MACOSX_BUNDLE_ICON_FILE ${PROJECT_NAME}.icns)
  set_source_files_properties (${ICON_FILE} PROPERTIES
    MACOSX_PACKAGE_LOCATION Resources)
endif ()

add_executable (${PROJECT_NAME}
  ${UI_SOURCES}
  ${MOC_SOURCES}
  ${QRC_SOURCES}
  ${SOURCES}
)

set_target_properties (${PROJECT_NAME} PROPERTIES CXX_STANDARD 17)

if (WIN32)
  set_target_properties (${PROJECT_NAME} PROPERTIES WIN32_EXECUTABLE true)
endif ()

if (APPLE)
  set_target_properties (${PROJECT_NAME} PROPERTIES MACOSX_BUNDLE true)
endif ()

target_link_libraries (${PROJECT_NAME} PRIVATE Qt${QT_VERSION_MAJOR}::Widgets)

if (CONFIG_XUNIQUE)
  target_link_libraries (${PROJECT_NAME} PRIVATE Qt${QT_VERSION_MAJOR}::Network)
endif ()

if (CONFIG_FLUIDSYNTH)
  target_link_libraries (${PROJECT_NAME} PRIVATE ${FLUIDSYNTH_LIBRARIES})
endif ()

if (CONFIG_ROUND)
  target_link_libraries (${PROJECT_NAME} PRIVATE ${MATH_LIBRARY})
endif ()


if (UNIX AND NOT APPLE)
  install (TARGETS ${PROJECT_NAME} RUNTIME
     DESTINATION ${CMAKE_INSTALL_BINDIR})
  install (FILES ${QM_FILES}
     DESTINATION ${CMAKE_INSTALL_DATADIR}/${PROJECT_NAME}/translations)
  install (FILES ${PROJECT_NAME}.desktop
     DESTINATION ${CMAKE_INSTALL_DATADIR}/applications)
  install (FILES images/${PROJECT_NAME}.png
     DESTINATION ${CMAKE_INSTALL_DATADIR}/icons/hicolor/32x32/apps)
  install (FILES images/${PROJECT_NAME}.svg
     DESTINATION ${CMAKE_INSTALL_DATADIR}/icons/hicolor/scalable/apps)
  install (FILES appdata/${PROJECT_NAME}.appdata.xml
     DESTINATION ${CMAKE_INSTALL_DATADIR}/metainfo)
endif ()

if (WIN32)
   install (TARGETS ${PROJECT_NAME} RUNTIME
     DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
   install (FILES ${QM_FILES}
     DESTINATION ${CMAKE_INSTALL_DATADIR}/${PROJECT_NAME}/translations)
endif ()
