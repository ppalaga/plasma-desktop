# vim:set softtabstop=3 shiftwidth=3 tabstop=3 expandtab:
project (KCMActivities)
add_definitions(-DTRANSLATION_DOMAIN=\"kcm_activities5\")

find_package (Qt5 REQUIRED NO_MODULE COMPONENTS Core Quick Sql Qml DBus)
find_package (KF5KCMUtils ${KF5_DEP_VERSION} CONFIG REQUIRED)
find_package (KF5Declarative ${KF5_DEP_VERSION} CONFIG REQUIRED)
find_package (KF5XmlGui ${KF5_DEP_VERSION} CONFIG REQUIRED)
find_package (KF5GlobalAccel ${KF5_DEP_VERSION} CONFIG REQUIRED)
find_package (KF5Activities ${KF5_DEP_VERSION} CONFIG REQUIRED)

set (KAMD_KCM_DATADIR
   ${KDE_INSTALL_DATADIR_KF5}/kactivitymanagerd/workspace/settings/
   )

if (NOT IS_ABSOLUTE "${KDE_INSTALL_DATADIR_KF5}")
   set (KAMD_KCM_DATADIR "${CMAKE_INSTALL_PREFIX}/${KAMD_KCM_DATADIR}")
endif ()

configure_file (kactivities-kcm-features.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/kactivities-kcm-features.h)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

add_subdirectory(imports)

set (KAMD_KCM_SRCS
   kcm_activities.cpp

   MainConfigurationWidget.cpp
   ActivitiesTab.cpp
   SwitchingTab.cpp
   PrivacyTab.cpp

   BlacklistedApplicationsModel.cpp
   ExtraActivitiesInterface.cpp

   utils/dbusfuture_p.cpp
   )

ki18n_wrap_ui (
   KAMD_KCM_SRCS
   ui/MainConfigurationWidgetBase.ui
   ui/PrivacyTabBase.ui
   ui/SwitchingTabBase.ui
   )

qt5_add_dbus_interface (
   KAMD_KCM_SRCS

   common/dbus/org.kde.ActivityManager.Features.xml
   features_interface
   )

add_library (kcm_activities MODULE ${KAMD_KCM_SRCS})

target_link_libraries (kcm_activities
   Qt5::Quick
   Qt5::Core
   Qt5::Sql
   Qt5::Qml
   Qt5::DBus
   KF5::KCMUtils
   KF5::I18n
   KF5::Service
   KF5::Declarative
   KF5::XmlGui
   KF5::GlobalAccel
   KF5::Activities
   )

install (
   TARGETS kcm_activities
   DESTINATION ${KDE_INSTALL_PLUGINDIR}
   )

install (
   FILES kcm_activities.desktop
   DESTINATION ${KDE_INSTALL_KSERVICES5DIR}
   )

install (
   DIRECTORY

   qml/activitiesTab
   qml/privacyTab

   DESTINATION ${KAMD_KCM_DATADIR}/qml
   )

