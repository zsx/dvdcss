# vim: ft=python expandtab

import os
from site_init import GBuilder, Initialize

opts = Variables()
opts.Add(PathVariable('PREFIX', 'Installation prefix', os.path.expanduser('~/FOSS'), PathVariable.PathIsDirCreate))
opts.Add(BoolVariable('DEBUG', 'Build with Debugging information', 0))
opts.Add(PathVariable('PERL', 'Path to the executable perl', r'C:\Perl\bin\perl.exe', PathVariable.PathIsFile))

env = Environment(variables = opts,
                  ENV=os.environ, tools = ['default', GBuilder])

Initialize(env)

PNG_VERSION_MAJOR=1
PNG_VERSION_MINOR=2
PNG_VERSION_RELEASE=10
PNG_VERSION_STRING="%d.%d.%d" % (PNG_VERSION_MAJOR, PNG_VERSION_MINOR, PNG_VERSION_RELEASE)

env['DOT_IN_SUBS'] = {'@VERSION@': PNG_VERSION_STRING,
                      '@prefix@': env['PREFIX'],
                      '@exec_prefix@': '${prefix}/bin',
                      '@libdir@': '${prefix}/lib',
                      '@includedir@': '${prefix}/include'}

env.AppendENVPath('PKGCONFIG_PATH', '$PREFIX/lib/pkgconfig')
env.Append(CPPPATH=['#', '#msvc'])
env.Append(CFLAGS=env['DEBUG_CFLAGS'])
env.Append(CPPDEFINES=['WIN32'] + env['DEBUG_CPPDEFINES'])
name = 'dvdcss'
libname = 'lib' + name

env.DotIn('libdvdcss.pc', 'src/libdvdcss.pc.in')
env.Alias('install', env.Install('$PREFIX/lib/pkgconfig', 'libdvdcss.pc'))

libdvdcss_SOURCES = map(lambda x: 'src/' + x, Split("libdvdcss.c device.c css.c ioctl.c error.c"))

dll = env.SharedLibrary([libname + env['LIB_SUFFIX'] + '.dll', name + '.lib'], libdvdcss_SOURCES + ['src/libdvdcss.def'])

env.AddPostAction(dll, 'mt.exe -nologo -manifest ${TARGET}.manifest -outputresource:$TARGET;2')

env.Alias('install', env.Install('$PREFIX/include/dvdcss', 'dvdcss.h'))
env.Alias('install', env.Install('$PREFIX/bin', libname + env['LIB_SUFFIX'] + '.dll'))
env.Alias('install', env.Install('$PREFIX/lib', name + '.lib'))
