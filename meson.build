# Note: on Mac, the Maya devkit .dylibs use @executable_path which works when Maya uses them.
# However, executables built with the dependencies declared in this file won't be able to find them by default.
# A workaround is to set these global environment variables:
#	DYLD_FALLBACK_LIBRARY_PATH=/Applications/Autodesk/maya2017/Maya.app/Contents/MacOS
#	DYLD_FALLBACK_FRAMEWORK_PATH=/Applications/Autodesk/maya2017/Maya.app/Contents/Frameworks
# In case built executables need to be "shipped", the alternative solution is to make copies of the Maya .dylibs
# they need (and optionally use otool to change the lookup logic in the copies) then ship them with the executable.

project('Maya-devkit','cpp')

compiler_id = meson.get_compiler('cpp').get_id()

maya_version = get_option('maya_version')
defines = [ '-DMAYA_VERSION='+maya_version ]
compile_args = [ ]

if build_machine.system()=='darwin'
	location = '/Applications/Autodesk/maya'+maya_version
	defines += [ '-DOSMac_' ]
elif build_machine.system()=='windows'
	if compiler_id!='msvc'
		error('Maya-devkit requires MSVC on Windows')
	endif
	location = 'C:/Program Files/Autodesk/maya'+maya_version
	defines += [ '-D_BOOL' ]
elif
	error('Platform not supported (yet)')
endif
defines += [ '-DMAYA_LOCATION="'+location+'"' ]
compile_args += defines
includes = include_directories(location + '/include')

if build_machine.system()=='darwin'
	mac_libs = location+'/Maya.app/Contents/MacOS'
	OpenMaya = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ '-L'+mac_libs, '-lOpenMaya' ] )
	OpenMayaAnim = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ '-L'+mac_libs, '-lOpenMayaAnim' ] )
	OpenMayaFX = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ '-L'+mac_libs, '-lOpenMayaFX' ] )
	OpenMayaRender = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ '-L'+mac_libs, '-lOpenMayaRender' ] )
	OpenMayaUI = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ '-L'+mac_libs, '-lOpenMayaUI' ] )
	Foundation = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ '-L'+mac_libs, '-lFoundation' ] )
elif build_machine.system()=='windows'
	windows_libs = join_paths(location,'lib')
	OpenMaya = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ join_paths(windows_libs, 'OpenMaya.lib') ] )
	OpenMayaAnim = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ join_paths(windows_libs, 'OpenMayaAnim.lib') ] )
	OpenMayaFX = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ join_paths(windows_libs, 'OpenMayaFX.lib') ] )
	OpenMayaRender = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ join_paths(windows_libs, 'OpenMayaRender.lib') ] )
	OpenMayaUI = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ join_paths(windows_libs, 'OpenMayaUI.lib') ] )
	Foundation = declare_dependency( include_directories: includes, compile_args: compile_args, link_args: [ join_paths(windows_libs, 'Foundation.lib') ] )
else
	error('System not supported yet')
endif

qt_plugins_dir = 'plugins'
if maya_version.to_int()<2020
	qt_plugins_dir = 'qt-plugins'
endif

if build_machine.system()=='darwin'
	test_environment = [
		'DYLD_FALLBACK_LIBRARY_PATH='+join_paths(location,'Maya.app/Contents/MacOS'),
		'DYLD_FALLBACK_FRAMEWORK_PATH='+join_paths(location,'Maya.app/Contents/Frameworks'),
		'QT_PLUGIN_PATH='+join_paths(location,qt_plugins_dir)
	]
else
	test_environment = [
		'QT_PLUGIN_PATH='+join_paths(location,qt_plugins_dir)
	]
endif
