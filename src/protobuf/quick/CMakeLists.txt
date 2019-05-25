set(TARGET protobufquickplugin)

set(TARGET_INCLUDE_DIR ${CMAKE_INSTALL_INCLUDEDIR}/${TARGET})

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

find_package(Qt5 COMPONENTS Core Qml REQUIRED)

execute_process(
    COMMAND ${QT_QMAKE_EXECUTABLE} -query QT_INSTALL_QML
    OUTPUT_VARIABLE TARGET_IMPORTS_DIR
    OUTPUT_STRIP_TRAILING_WHITESPACE
)

file(GLOB SOURCES
    qtprotobufquickplugin.cpp
    qtprotobufvaluetypes.cpp)

file(GLOB HEADERS
    qtprotobufquickplugin.h
    qtprotobufvaluetypes_p.h
    qtprotobufquick_global.h)

add_library(${TARGET} SHARED ${SOURCES})
target_link_libraries(${TARGET} PRIVATE Qt5::Core Qt5::Qml ${QTPROTOBUF_COMMON_NAMESPACE}::QtProtobuf)
set_target_properties(${TARGET} PROPERTIES
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/QtProtobuf")
target_compile_definitions(${TARGET} PRIVATE QTPROTOBUFQUICK_LIB)
target_include_directories(${TARGET} PRIVATE ${Qt5Qml_PRIVATE_INCLUDE_DIRS})
install(TARGETS ${TARGET}
    PUBLIC_HEADER DESTINATION ${TARGET_INCLUDE_DIR}
    LIBRARY DESTINATION ${TARGET_IMPORTS_DIR})

add_custom_command(TARGET ${TARGET}
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_CURRENT_SOURCE_DIR}/qmldir $<TARGET_FILE_DIR:${TARGET}>/qmldir
    COMMENT "Copying qmldir to binary directory")

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/qmldir DESTINATION "${TARGET_IMPORTS_DIR}/QtProtobuf")