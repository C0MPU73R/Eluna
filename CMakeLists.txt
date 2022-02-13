#
# Copyright (C) 2010 - 2016 Eluna Lua Engine <http://emudevs.com/>
# This program is free software licensed under GPL version 3
# Please see the included DOCS/LICENSE.md for more information
#

macro(get_all_targets_recursive targets dir)
    get_property(subdirectories DIRECTORY ${dir} PROPERTY SUBDIRECTORIES)
    foreach(subdir ${subdirectories})
        get_all_targets_recursive(${targets} ${subdir})
    endforeach()

    get_property(current_targets DIRECTORY ${dir} PROPERTY BUILDSYSTEM_TARGETS)
    list(APPEND ${targets} ${current_targets})
endmacro()

if( ${CMAKE_PROJECT_NAME} STREQUAL "TrinityCore" )
	# We only want to include everything in the TrinityCore subfolder
	file(GLOB removal_list
		${CMAKE_CURRENT_SOURCE_DIR}/Mangos/*
		${CMAKE_CURRENT_SOURCE_DIR}/CMangos/*
		${CMAKE_CURRENT_SOURCE_DIR}/AzerothCore/*
	)
	
	get_all_targets_recursive(all_targets ${CMAKE_SOURCE_DIR})
	foreach(target ${all_targets})
	  if (${target} IN_LIST removal_list)
		continue()
	  else()
		get_target_property(_sources ${target} SOURCES)
		if(_sources)
		  foreach(item ${removal_list})
			list(REMOVE_ITEM _sources ${item})
		  endforeach()
		  set_property(TARGET ${target} PROPERTY SOURCES ${_sources})
		endif()
		get_target_property(_includes ${target} INCLUDE_DIRECTORIES)
		if(_includes)
		  foreach(dir ${removal_list})
			list(REMOVE_ITEM _includes ${dir})
		  endforeach()
		  set_property(TARGET ${target} PROPERTY INCLUDE_DIRECTORIES ${_includes})
		endif()
	  endif()
	endforeach()
endif()