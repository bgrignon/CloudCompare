option(PLUGIN_IO_QLAS "LAS IO plugin using laszip" OFF)

if (PLUGIN_IO_QLAS)
    project(QLAS_IO_PLUGIN)

    AddPlugin(NAME ${PROJECT_NAME} TYPE io)

    list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
    find_package(LASzip MODULE REQUIRED)

    target_compile_definitions(${PROJECT_NAME} PRIVATE -DQT_FORCE_ASSERTS)

    target_link_libraries(${PROJECT_NAME} LASzip::LASzip)

    add_subdirectory(include)
    add_subdirectory(src)
    add_subdirectory(ui)

    #####################################################################################
    # Generating an export header for the symbols from qLAS used by qAnnotationTool and 
    # qFileScroller.
    # Modification of the CMakeLists.txt and relevant code modification made by Baptiste
    # Grignon, 2025. Original license (GPLv2) apply.

    include(GenerateExportHeader)

    generate_export_header(${PROJECT_NAME}
        EXPORT_FILE_NAME ${CMAKE_CURRENT_BINARY_DIR}/exports/QLasExport.h
        EXPORT_MACRO_NAME QLAS_IO_PLUGIN_API
    )

    target_sources(${PROJECT_NAME}
        PRIVATE
            ${CMAKE_CURRENT_BINARY_DIR}/exports/QLasExport.h
    )

    #install(
    #    FILES
    #        ${CMAKE_CURRENT_BINARY_DIR}/exports/QLasExport.h
    #    DESTINATION
    #        include/${PROJECT_NAME}
    #)

    target_include_directories(${PROJECT_NAME}
        PUBLIC
            $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/exports>
            #$<INSTALL_INTERFACE:include/${PROJECT_NAME}>
    )
    #####################################################################################

    if (WIN32)
        copy_files( "${LASZIP_DLL}" "${CLOUDCOMPARE_DEST_FOLDER}" 1 )
        if (${OPTION_BUILD_CCVIEWER})
            copy_files( "${LASZIP_DLL}" "${CCVIEWER_DEST_FOLDER}" 1 )
		endif()
    endif ()
endif ()
