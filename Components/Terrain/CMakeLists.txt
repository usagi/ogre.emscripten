#-------------------------------------------------------------------
# This file is part of the CMake build system for OGRE
#     (Object-oriented Graphics Rendering Engine)
# For the latest info, see http://www.ogre3d.org/
#
# The contents of this file are placed in the public domain. Feel
# free to make use of it in any way you like.
#-------------------------------------------------------------------

############################################################
# Terrain optional component
############################################################

PROJECT(OgreTerrain)

# define header and source files for the library
set (HEADER_FILES
    include/OgreTerrain.h
    include/OgreTerrainGroup.h
	include/OgreTerrainLayerBlendMap.h
	include/OgreTerrainMaterialGenerator.h
	include/OgreTerrainMaterialGeneratorA.h
	include/OgreTerrainPrerequisites.h
    include/OgreTerrainQuadTreeNode.h
)

set (SOURCE_FILES
	src/OgreTerrain.cpp
    src/OgreTerrainGroup.cpp
	src/OgreTerrainLayerBlendMap.cpp
	src/OgreTerrainMaterialGenerator.cpp
	src/OgreTerrainMaterialGeneratorA.cpp
	src/OgreTerrainQuadTreeNode.cpp
)


# Add needed definitions 
add_definitions(-DOGRE_TERRAIN_EXPORTS -D_MT -D_USRDLL)

if (OGRE_BUILD_COMPONENT_PAGING)
  set (HEADER_FILES ${HEADER_FILES}
	include/OgreTerrainPaging.h
	include/OgreTerrainPagedWorldSection.h
  )
  set (SOURCE_FILES ${SOURCE_FILES}
	src/OgreTerrainPaging.cpp
	src/OgreTerrainPagedWorldSection.cpp
  )
  include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../Paging/include)
endif ()

# include headers
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
include_directories(${OGRE_SOURCE_DIR}/OgreMain/include)

# setup target
ogre_add_library(OgreTerrain ${OGRE_COMP_LIB_TYPE} ${HEADER_FILES} ${SOURCE_FILES} ${PLATFORM_HEADER_FILES} ${PLATFORM_SOURCE_FILES})
set_target_properties(OgreTerrain PROPERTIES VERSION ${OGRE_SOVERSION})
target_link_libraries(OgreTerrain OgreMain)
if (OGRE_BUILD_COMPONENT_PAGING)
	target_link_libraries(OgreTerrain OgrePaging)
endif ()
if (OGRE_CONFIG_THREADS)
  target_link_libraries(OgreTerrain ${Boost_LIBRARIES})
endif ()


# install 
ogre_config_component(OgreTerrain)

if (APPLE AND NOT OGRE_BUILD_PLATFORM_APPLE_IOS)
    # Set the INSTALL_PATH so that Components can be installed in the application package
    set_target_properties(OgreTerrain
       PROPERTIES BUILD_WITH_INSTALL_RPATH 1
       INSTALL_NAME_DIR "@executable_path/../Components"
    )

	# Copy headers into the main Ogre framework
	add_custom_command(TARGET OgreTerrain POST_BUILD
	  COMMAND /Developer/Library/PrivateFrameworks/DevToolsCore.framework/Resources/pbxcp ARGS -exclude .DS_Store -exclude .hg -exclude 'CMakeLists.txt' -resolve-src-symlinks ${CMAKE_CURRENT_SOURCE_DIR}/include/*.h ${OGRE_BINARY_DIR}/lib/$(CONFIGURATION)/Ogre.framework/Headers/
	)
endif()

install(FILES ${HEADER_FILES}
  DESTINATION include/OGRE/Terrain
)

