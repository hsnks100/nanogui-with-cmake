CMAKE_MINIMUM_REQUIRED ( VERSION 2.8 )
include(${CMAKE_ROOT}/Modules/ExternalProject.cmake)

PROJECT ( qc )
SET ( SRC_FILES 
    main.cpp

    )
SET ( PROJECT_VERSION_MAJOR 0 )
SET ( PROJECT_VERSION_MINOR 1 )
 
SET ( CMAKE_BUILD_TYPE Release )
SET ( CMAKE_VERBOSE_MAKEFILE true )
 
SET ( OUTPUT_ELF
        "${CMAKE_PROJECT_NAME}-${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.out"
        ) 

SET ( CMAKE_C_FLAGS_RELEASE "-static" )



ExternalProject_Add( nanogui
        PREFIX third_party_src/nanogui
        INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}/third_party/nanogui
        GIT_REPOSITORY https://github.com/wjakob/nanogui.git
        BUILD_IN_SOURCE 1 
        CONFIGURE_COMMAND cmake -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR> -DNANOGUI_BUILD_SHARED=NO -DNANOGUI_BUILD_EXAMPLE=NO
        INSTALL_COMMAND make install
        )

find_package(PkgConfig)
pkg_check_modules(X11 x11)
pkg_check_modules(XINERAMA xinerama)
pkg_check_modules(XF86 xxf86vm)
pkg_check_modules(XRANDR xrandr)
pkg_check_modules(XCURSOR xcursor)
pkg_check_modules(OPENGL gl)
INCLUDE_DIRECTORIES ( 
    third_party/nanogui/include
    third_party_src/nanogui/src/nanogui/ext/eigen
    third_party_src/nanogui/src/nanogui/ext/nanovg/src
    
    )

ADD_EXECUTABLE( ${OUTPUT_ELF} ${SRC_FILES} ) 
TARGET_LINK_LIBRARIES(${OUTPUT_ELF}
    ${CMAKE_SOURCE_DIR}/third_party/nanogui/lib/libnanogui.a
    dl
    pthread
    ${X11_LIBRARIES}
    ${XF86_LIBRARIES}
    ${OPENGL_LIBRARIES}
    ${XRANDR_LIBRARIES}
    ${XCURSOR_LIBRARIES}
    ${XINERAMA_LIBRARIES} 
    )
