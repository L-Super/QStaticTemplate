# configure version
configure_file(version.h.in ${CMAKE_CURRENT_SOURCE_DIR}/version.h @ONLY)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS
        Core
        Gui
        Widgets
)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS
        Core
        Gui
        Widgets
        REQUIRED)

qt_add_resources(QRC_FILES resources.qrc)

set(SRC_FILES MainWindow.cpp MainWindow.h MainWindow.ui)

if (WIN32)
    if (MSVC)
        # Set MSVC to use utf-8 encode for building
        add_compile_options(/utf-8)
    endif ()

    # set Windows rc info
    set(PRODUCT_ICON "logo.ico")
    set(FILE_VERSION ${PROJECT_VERSION_MAJOR},${PROJECT_VERSION_MINOR},${PROJECT_VERSION_PATCH},${PROJECT_VERSION_TWEAK}) # 文件版本
    set(FILE_VERSION_STR "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}.${PROJECT_VERSION_TWEAK}")
    set(PRODUCT_VERSION ${PROJECT_VERSION_MAJOR},${PROJECT_VERSION_MINOR},${PROJECT_VERSION_PATCH},${PROJECT_VERSION_TWEAK}) # 产品版本
    set(PRODUCT_VERSION_STR "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}.${PROJECT_VERSION_TWEAK}")
    set(COMPANY_NAME "Qt")
    set(FILE_DESCRIPTION "Qt template application")
    set(LEGAL_COPYRIGHT "Copyright 2024-2077 The L Company Ltd.")
    set(PRODUCT_NAME "Qt template")

    set(WIN_RES_PATH ${CMAKE_CURRENT_SOURCE_DIR}/resources/win)
    configure_file(${WIN_RES_PATH}/app.rc.in ${WIN_RES_PATH}/app.rc @ONLY)

    set(QRC_FILES ${QRC_FILES} ${WIN_RES_PATH}/app.rc)
endif ()

add_executable(${PROJECT_NAME}
        main.cpp
        ${QRC_FILES}
        ${SRC_FILES}
)

if (WIN32)
    if (CMAKE_BUILD_TYPE STREQUAL "Debug")
        message(STATUS "Running ${CMAKE_BUILD_TYPE} mode")
    else ()
        message(STATUS "Running ${CMAKE_BUILD_TYPE} mode")
        # Remove the terminal display
        set_target_properties(${PROJECT_NAME} PROPERTIES WIN32_EXECUTABLE TRUE)
    endif ()
endif ()

target_link_libraries(${PROJECT_NAME}
        Qt${QT_VERSION_MAJOR}::Core
        Qt${QT_VERSION_MAJOR}::Gui
        Qt${QT_VERSION_MAJOR}::Widgets
        -static-libgcc
        -static-libstdc++
        -static # static link libgcc_s_seh-1 libstdc++ libwinpthread
)
