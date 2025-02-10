cmake_minimum_required(VERSION 3.14)
project(txtedit VERSION 1.0)

# Set C standard
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Enable FetchContent for downloading dependencies
include(FetchContent)

# Find GTK3
find_package(PkgConfig REQUIRED)
pkg_check_modules(GTK3 REQUIRED gtk+-3.0)

# Include directories
include_directories(${GTK3_INCLUDE_DIRS})
link_directories(${GTK3_LIBRARY_DIRS})
add_definitions(${GTK3_CFLAGS_OTHER} -DLIBDEFLATE_DLL)

# Define the executable
add_executable(txtedit main.c)

# Link required libraries
target_link_libraries(txtedit ${GTK3_LIBRARIES} 
    z gdi32 imm32 shell32 ole32 uuid winmm dwmapi setupapi cfgmgr32 hid 
    winspool comctl32 comdlg32 pangocairo-1.0 pangowin32-1.0 pango-1.0 
    cairo-gobject cairo harfbuzz gdk_pixbuf-2.0 atk-1.0 gio-2.0 
    gobject-2.0 glib-2.0 intl
)

# Add Windows-specific linker flag for Release mode
if(CMAKE_BUILD_TYPE STREQUAL "Release")
    target_link_libraries(txtedit -mwindows)
endif()

# Set output directory
set_target_properties(txtedit PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Define installation rules
install(TARGETS txtedit RUNTIME DESTINATION bin)

# Enable CPack for packaging
include(CPack)

set(CPACK_PACKAGE_NAME "txtedit")
set(CPACK_PACKAGE_VERSION "1.0")
set(CPACK_GENERATOR "ZIP;NSIS")
set(CPACK_PACKAGE_DESCRIPTION "A simple text editor built with GTK3")
set(CPACK_PACKAGE_VENDOR "YourCompany")
set(CPACK_PACKAGE_CONTACT "your@email.com")
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE")

# Add a custom target to run the executable
add_custom_target(run
    COMMAND ${CMAKE_BINARY_DIR}/bin/txtedit
    DEPENDS txtedit
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/bin
)
