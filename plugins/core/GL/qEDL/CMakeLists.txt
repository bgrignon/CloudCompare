option( PLUGIN_GL_QEDL "Install qEDL plugin" OFF )

if( PLUGIN_GL_QEDL )
	project( QEDL_GL_PLUGIN )
	
	AddPlugin(
		NAME ${PROJECT_NAME}
		TYPE gl
		SHADER_FOLDER EDL
	)
	
	add_subdirectory( include )
	add_subdirectory( src )
	add_subdirectory( shaders )

	#####################################################################################
    # Generating an export header for the symbols from qEDL used by qAnnotationTool and 
    # qFileScroller.
    # Modification of the CMakeLists.txt and relevant code modification made by Baptiste
    # Grignon, 2025. Original license (GPLv2) apply.

    include(GenerateExportHeader)

    generate_export_header(${PROJECT_NAME}
        EXPORT_FILE_NAME ${CMAKE_CURRENT_BINARY_DIR}/exports/QEdlExport.h
        EXPORT_MACRO_NAME QEDL_GL_PLUGIN_API
    )

    target_sources(${PROJECT_NAME}
        PRIVATE
            ${CMAKE_CURRENT_BINARY_DIR}/exports/QEdlExport.h
    )

    #install(
    #    FILES
    #        ${CMAKE_CURRENT_BINARY_DIR}/exports/QEdlExport.h
    #    DESTINATION
    #        include/${PROJECT_NAME}
    #)

    target_include_directories(${PROJECT_NAME}
        PUBLIC
            $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/exports>
            #$<INSTALL_INTERFACE:include/${PROJECT_NAME}>
    )
	#####################################################################################

endif()
