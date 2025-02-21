set(PROJECT_NAME DeepSkyStackerLive)
message("Configuring project: " ${PROJECT_NAME})

set(COMPILE_WARNING_AS_ERROR YES)
SET (CMAKE_INSTALL_BINDIR ".")

qt_standard_project_setup()

set(CMAKE_AUTORCC ON)

################################################################################
# Source groups
################################################################################
set(Header_Files
    "dssliveenums.h"
    "LiveSettings.h"
 )
source_group("Header Files" FILES ${Header_Files})

if(WIN32)
set(Header_Files__SmtpMime
    "../include/SmtpMime/emailaddress.h"
    "../include/SmtpMime/mimeattachment.h"
    "../include/SmtpMime/mimebase64encoder.h"
    "../include/SmtpMime/mimebase64formatter.h"
    "../include/SmtpMime/mimebytearrayattachment.h"
    "../include/SmtpMime/mimecontentencoder.h"
    "../include/SmtpMime/mimefile.h"
    "../include/SmtpMime/mimehtml.h"
    "../include/SmtpMime/mimeinlinefile.h"
    "../include/SmtpMime/mimemessage.h"
    "../include/SmtpMime/mimemultipart.h"
    "../include/SmtpMime/mimepart.h"
    "../include/SmtpMime/mimeqpencoder.h"
    "../include/SmtpMime/mimeqpformatter.h"
    "../include/SmtpMime/mimetext.h"
    "../include/SmtpMime/quotedprintable.h"
    "../include/SmtpMime/SmtpMime"
    "../include/SmtpMime/smtpmime_global.h"
)
source_group("Header Files\\SmtpMime" FILES ${Header_Files__SmtpMime})
endif()

set(Source_Files
    "ChartTab.cpp"
    "DeepSkyStackerLive.cpp"
    "EmailSettings.cpp"
    "fileregistrar.cpp"
    "filestacker.cpp"
    "foldermonitor.cpp"
    "footprint.cpp"
    "imageviewer.cpp"
    "LiveSettings.cpp"
    "progresslive.cpp"
    "RestartMonitoring.cpp"
    "settingstab.cpp"
)
source_group("Source Files" FILES ${Source_Files})

set(Tools
    "../Tools/imageview.cpp"
    "../Tools/QLinearGradientCtrl.cpp"
)
source_group("Tools" FILES ${Tools})

set(i18n_Files
	"i18n/DSSLive_ca.ts"
	"i18n/DSSLive_cs.ts"
	"i18n/DSSLive_de.ts"
	"i18n/DSSLive_en.ts"
	"i18n/DSSLive_es.ts"
	"i18n/DSSLive_fr.ts"
	"i18n/DSSLive_it.ts"
	"i18n/DSSLive_ja_JP.ts"
	"i18n/DSSLive_nl.ts"
	"i18n/DSSLive_pt_BR.ts"
	"i18n/DSSLive_ro.ts"
	"i18n/DSSLive_ru.ts"
	"i18n/DSSLive_tr.ts"
	"i18n/DSSLive_zh_CN.ts"
	"i18n/DSSLive_zh_TW.ts"
	)	
source_group("Qt\\i18n" FILES ${i18n_Files} "i18n/translations.qrc"
    )
	
set(Resources
	"../DeepSkyStacker/Icon.rc"
	"../DeepSkyStacker/Icon/app.ico"
	"resources.qrc"
	"version.rc"
	)
source_group("Resources" FILES ${Resources}	)

set(ALL_FILES
    ${Header_Files}
    ${Header_Files__SmtpMime}
    ${Source_Files}
	${i18n_Files} "i18n/translations.qrc"
	${Resources}
    ${Tools}
)

################################################################################
# Target
################################################################################
qt_add_executable(${PROJECT_NAME} ${ALL_FILES})

set_source_files_properties(${i18n_Files}
     PROPERTIES OUTPUT_LOCATION "${CMAKE_CURRENT_SOURCE_DIR}/i18n")

qt_add_lrelease(TS_FILES ${i18n_Files})

set_target_properties(DeepSkyStackerLive PROPERTIES
    INTERPROCEDURAL_OPTIMIZATION_RELEASE "TRUE"
)
set_target_properties(DeepSkyStackerLive PROPERTIES
    WIN32_EXECUTABLE ON
    MACOSX_BUNDLE ON
)

################################################################################
# Output directory
################################################################################
set_target_properties(${PROJECT_NAME} PROPERTIES
    OUTPUT_DIRECTORY_DEBUG   "${CMAKE_SOURCE_DIR}/${CMAKE_VS_PLATFORM_NAME}/$<CONFIG>/"
    OUTPUT_DIRECTORY_RELEASE "${CMAKE_SOURCE_DIR}/${CMAKE_VS_PLATFORM_NAME}/$<CONFIG>/"
)

################################################################################
# Include directories
################################################################################
target_include_directories(${PROJECT_NAME} PUBLIC
    .
    ../DeepSkyStackerKernel
    ../LibRaw
    ../ZClass
    ../Tools
    ../SmtpClient-for-Qt/src
  )

if(WIN32)
target_include_directories(${PROJECT_NAME} PUBLIC
   ../Zlib
   ../LibTiff
   ../CFitsIO
   ../include
   ../include/exiv2
   ../include/SmtpMime
   "$<$<CONFIG:Debug>:C:/Program Files (x86)/Visual Leak Detector/include>"
   "$ENV{Boost_1_80_0}"
)
else()
    target_include_directories(DeepSkyStackerCL PUBLIC
    /usr/include/cfitsio
    )
endif()

target_precompile_headers(${PROJECT_NAME} PRIVATE
	stdafx.h
	)

################################################################################
# Compile definitions
################################################################################
target_compile_definitions(${PROJECT_NAME} PRIVATE
  "$<$<CONFIG:Debug>:Z_TRACE_DEVELOP>"
  "$<$<CONFIG:Release>:NDEBUG>"
  NOMINMAX
  LIBRAW_NODLL
  _CRT_SECURE_NO_DEPRECATE
  USE_LIBTIFF_STATIC
)

################################################################################
# Compile and link options
################################################################################
if(MSVC)
    target_compile_options(${PROJECT_NAME} PRIVATE
        $<$<CONFIG:Debug>:
            /Gm-;
            /Zi
        >
        $<$<CONFIG:Release>:
            /Zi;
            /GS-
        >
	    -W4 /wd4652 /wd4702 /wd4828 
		-Zc:__cplusplus /openmp:experimental
        /MP;
        /GL-;
        /EHsc
    )
    target_link_options(${PROJECT_NAME} PRIVATE
        $<$<CONFIG:Debug>:
            /PROFILE
        >
        $<$<CONFIG:Release>:
            /OPT:ICF
        >
        /DEBUG:FULL;
        /MACHINE:X64;
        /SUBSYSTEM:WINDOWS;
        /DYNAMICBASE:NO
    )
endif()

if(WIN32)
target_link_directories(${PROJECT_NAME} PRIVATE
	"$<$<CONFIG:Debug>:C:/Users/amonra/Documents/GitHub/DSS/libs/Win64/DebugLibs>"
	"$<$<CONFIG:Release>:C:/Users/amonra/Documents/GitHub/DSS/libs/Win64/ReleaseLibs>"
	"$<$<CONFIG:Debug>:C:/Program Files (x86)/Visual Leak Detector/lib/Win64>"
	)
endif()

################################################################################
# Dependencies
################################################################################
# Link with other targets.
target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt6::Core
    Qt6::Widgets
    Qt6::Network
	Qt6::Charts
	DeepSkyStackerKernel
    ZClass    
)
target_link_libraries(${PROJECT_NAME} PRIVATE libraw::libraw)
target_link_libraries(${PROJECT_NAME} PRIVATE exiv2)
target_link_libraries(${PROJECT_NAME} PRIVATE tiff)
target_link_directories(${PROJECT_NAME} PRIVATE SmtpClient-for-Qt/src)
target_link_libraries(${PROJECT_NAME} PRIVATE SmtpMime)
target_link_libraries(${PROJECT_NAME} PRIVATE cfitsio)
# set(DEBUG_DEPENDENCIES exiv2d libexpatd zlibstaticd)
# set(RELEASE_DEPENDENCIES exiv2 libexpat zlibstatic)

# set(ADDITIONAL_LIBRARY_DEPENDENCIES
#     "$<$<CONFIG:Debug>:${DEBUG_DEPENDENCIES}>"
#     "$<$<CONFIG:Release>:${RELEASE_DEPENDENCIES}>"
#     "cfitsio"
#     SmtpMime
# )

if(WIN32)
set(ADDITIONAL_LIBRARY_DEPENDENCIES ${ADDITIONAL_LIBRARY_DEPENDENCIES}
    user32
    psapi
    htmlhelp
)
endif()

target_link_libraries(${PROJECT_NAME} PRIVATE "${ADDITIONAL_LIBRARY_DEPENDENCIES}")

#
# Force the values of QT_DEPLOY... variables
#
set (QT_DEPLOY_BIN_DIR ".")
set (QT_DEPLOY_LIBEXEC_DIR ".")
set (QT_DEPLOY_LIB_DIR ".")
set (QT_DEPLOY_PLUGINS_DIR ".")

#
# Set options for the deployment script.   The value of:
# 
# --libdir .
# 
# is set to work around a bug in qt_deploy_runtime_dependencies which uses
#
# --libdir bin
# 
# even though QT_DEPLOY_LIB_DIR is set to .
#
set (deploy_tool_options_arg "--libdir .")
if(APPLE)
    set(deploy_tool_options_arg "${deploy_tool_options_arg} --hardened-runtime")
elseif(WIN32)
    set(deploy_tool_options_arg "${deploy_tool_options_arg} --pdb")
endif()

# Generate a deployment script to be executed at install time
# App bundles on macOS have an .app suffix
if(APPLE)
    set(executable_path "$<TARGET_FILE_NAME:DeepSkyStackerLive>.app")
else()
    message ("Qt Deploy Bin Dir: " ${QT_DEPLOY_BIN_DIR})
    message ("Qt Deploy Lib Dir: " ${QT_DEPLOY_LIB_DIR})
    message ("Target filename:"  $<TARGET_FILE_NAME:DeepSkyStackerLive>)
    set(executable_path "${QT_DEPLOY_BIN_DIR}/$<TARGET_FILE_NAME:DeepSkyStackerLive>")
endif()
message ("executable_path: " ${executable_path})
message ("deploy tools options arg: " ${deploy_tool_options_arg})
 qt_generate_deploy_script(
     TARGET DeepSkyStackerLive
     OUTPUT_SCRIPT deploy_script
     CONTENT "
 qt_deploy_runtime_dependencies(
     EXECUTABLE \"${executable_path}\"
     DEPLOY_TOOL_OPTIONS ${deploy_tool_options_arg}
 )"
 )

INSTALL (TARGETS DeepSkyStackerLive)
INSTALL (SCRIPT ${deploy_script})
