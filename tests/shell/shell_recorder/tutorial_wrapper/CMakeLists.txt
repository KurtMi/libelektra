function (add_s_test NAME FILE)
	add_test (
		NAME testshell_markdown_${NAME}
		COMMAND "${CMAKE_CURRENT_BINARY_DIR}/markdown_shell_recorder.sh" "${FILE}"
		WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}"
		)
	set_property(TEST testshell_markdown_${NAME} PROPERTY LABELS memleak kdbtests)
endfunction ()

function (add_plugin_shell_test PLUGIN)
	list (FIND REMOVED_PLUGINS ${PLUGIN} INDEX)
	if (${INDEX} EQUAL -1)
		add_s_test (${PLUGIN} "${CMAKE_SOURCE_DIR}/src/plugins/${PLUGIN}/README.md")
	endif ()
endfunction ()

# Only add test below if INI plugin is available
list (FIND REMOVED_PLUGINS mini PLUGIN_INDEX_INI)
if (${PLUGIN_INDEX_INI} EQUAL -1)
	add_s_test (msr_syntax "${CMAKE_SOURCE_DIR}/tests/shell/shell_recorder/tutorial_wrapper/SyntaxCheck.md")
endif (${PLUGIN_INDEX_INI} EQUAL -1)

# Only add test below if Spec plugin is available
list (FIND REMOVED_PLUGINS spec PLUGIN_INDEX_SPEC)
if (${PLUGIN_INDEX_SPEC} EQUAL -1)
	add_s_test (kdb-global-umount "${CMAKE_SOURCE_DIR}/doc/help/kdb-global-umount.md")
endif (${PLUGIN_INDEX_SPEC} EQUAL -1)

add_s_test (readme_msr "${CMAKE_SOURCE_DIR}/tests/shell/shell_recorder/tutorial_wrapper/README.md")
add_s_test (issue_template "${CMAKE_SOURCE_DIR}/.github/ISSUE_TEMPLATE.md")
add_s_test (tutorial_cascading "${CMAKE_SOURCE_DIR}/doc/tutorials/cascading.md")
add_s_test (kdb-complete "${CMAKE_SOURCE_DIR}/doc/help/kdb-complete.md")
add_s_test (kdb-ls "${CMAKE_SOURCE_DIR}/doc/help/kdb-ls.md")

add_plugin_shell_test (blockresolver)
add_plugin_shell_test (boolean)
add_plugin_shell_test (csvstorage)
add_plugin_shell_test (cachefilter)
add_plugin_shell_test (camel)
add_plugin_shell_test (conditionals)
add_plugin_shell_test (directoryvalue)
add_plugin_shell_test (enum)
add_plugin_shell_test (hosts)
add_plugin_shell_test (ini)
add_plugin_shell_test (ipaddr)
add_plugin_shell_test (line)
add_plugin_shell_test (mathcheck)
add_plugin_shell_test (mozprefs)
add_plugin_shell_test (multifile)
add_plugin_shell_test (range)
add_plugin_shell_test (type)
add_plugin_shell_test (xerces)
add_plugin_shell_test (yajl)

# The tests below might fail on Linux if ASAN is enabled because of problematic code in `stl_tree.h`
# The cause of this seems to be a bug in old versions of `libstdc++`:
# http://lists.llvm.org/pipermail/cfe-dev/2013-August/031194.html
set (ASAN_LINUX ENABLE_ASAN AND ${CMAKE_SYSTEM_NAME} MATCHES "Linux")
if (NOT (${ASAN_LINUX} AND CMAKE_CXX_COMPILER_ID MATCHES "Clang" AND CMAKE_CXX_COMPILER_VERSION VERSION_LESS 5))
	list (FIND REMOVED_PLUGINS validation PLUGIN_INDEX_VALIDATION)
	if (${PLUGIN_INDEX_VALIDATION} EQUAL -1)
		add_s_test (tutorial_validation "${CMAKE_SOURCE_DIR}/doc/tutorials/validation.md")
	endif (${PLUGIN_INDEX_VALIDATION} EQUAL -1)

	add_plugin_shell_test (base64)
	add_plugin_shell_test (dini)
	add_plugin_shell_test (mini)
	add_plugin_shell_test (tcl)
	add_plugin_shell_test (yamlcpp)
endif (NOT (${ASAN_LINUX} AND CMAKE_CXX_COMPILER_ID MATCHES "Clang" AND CMAKE_CXX_COMPILER_VERSION VERSION_LESS 5))

# Only works with super user privileges, since it writes to `/etc/hosts`:
# add_s_test (tutorial_mount "${CMAKE_SOURCE_DIR}/doc/tutorials/mount.md")
