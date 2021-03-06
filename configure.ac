dnl Process this file with autoconf to create configure.

AC_INIT
AC_CONFIG_SRCDIR([src/ferite.c])
AM_INIT_AUTOMAKE(ferite, 1.1.18)
AM_CONFIG_HEADER(config.h)

AC_PROG_CC
AM_PROG_CC_STDC
AC_C_CONST

AC_PATH_PROG(unamepath, uname)
if test "_$unamepath" = _; then
   system="unknown"
else                       
   AC_MSG_CHECKING(system type)
   system=`$unamepath -s`
   if test "$system" = "Linux"; then
	  AC_DEFINE([USING_LINUX], 1, [We Are Using Linux])
      AC_MSG_RESULT($system)
   fi
   if test "$system" = "FreeBSD"; then
	  AC_DEFINE([USING_FREEBSD], 1, [We Are Using FreeBSD])
      AC_MSG_RESULT($system)
   fi
   if test "$system" = "SunOS"; then
      AC_DEFINE([USING_SOLARIS], 1, [We Are Using Solaris])
      AC_MSG_RESULT($system)
   fi
	if test "$system" = "Darwin"; then
		osx_version=`$unamepath -r | cut -c 1-2`
		if test "$osx_version" = "10"; then
            AC_MSG_RESULT("Max OS X Snow Leopard ($system)")
			AC_DEFINE([USE_SNOWLEOPARD], 1, [Whether to use snow leopard])
		else
     		AC_DEFINE([USING_DARWIN], 1, [We Are Using darwin - the unix part of MacOSX])
            AC_MSG_RESULT($system)
		fi
	fi
   if test "$system" = "OpenBSD"; then
      AC_DEFINE([USING_OPENBSD], 1, [We Are Using OpenBSD])
      AC_MSG_RESULT($system)
   fi
   if test "$system" = "NetBSD"; then
      AC_DEFINE([USING_NETBSD], 1, [We Are Using NetBSD])
      AC_MSG_RESULT($system)
   fi
   
   cygwin=`$unamepath -s | cut -c 1-6`
   if test "$cygwin" = "CYGWIN"; then
      AC_DEFINE([USING_CYGWIN], 1, [We Are Using CygWin])
	  system="CYGWIN"
      AC_MSG_RESULT($system)
   fi
   mingw=`$unamepath -s | cut -c 1-5`
   if test "$mingw" = "MINGW"; then
	  AC_DEFINE([USING_MINGW], 1, [We Are Using MinGW])
	  system="MINGW"
      AC_MSG_RESULT($system)
   fi
			
   machinetype=`$unamepath -m`
   AC_MSG_CHECKING(machine type)
   AC_MSG_RESULT($machinetype)
   AC_DEFINE_UNQUOTED([MACH_TYPE], "$machinetype", [The machine type we are building on])

   machineos=`$unamepath -s`
   AC_MSG_CHECKING(operating system)
   AC_MSG_RESULT($machineos)
   AC_DEFINE_UNQUOTED([OS_NAME], "$machineos", [The os we are running on])
                              
   machinever=`$unamepath -r`
   AC_MSG_CHECKING(opertating system version)
   AC_MSG_RESULT($machinever)
   AC_DEFINE_UNQUOTED([OS_RELEASE], "$machinever", [The release verison of the os])
fi

if test "x$prefix" = "xNONE"; then
  prefix="/usr/local"
else
  prefix=$prefix
fi

AC_CANONICAL_HOST
#dnl platform=`$unamepath -s -m | sed -e "s/ /-/g;"`
platform="$host_os-$host_cpu"
AC_MSG_CHECKING(platform)
AC_MSG_RESULT($platform)
AC_DEFINE_UNQUOTED([PLATFORM], "$platform", [The Platform on which we exist])

CFLAGS="-DHAVE_CONFIG_HEADER -L$prefix/lib $CFLAGS"
CCFLAGS="-DHAVE_CONFIG_HEADER  -L$prefix/lib $CCFLAGS"

AM_CONDITIONAL(NEED_FERITE_LIB, test "$system" = "Darwin" -o "$system" = "CYGWIN" -o "$system" = "SunOS" -o "$system" = "MINGW")

AC_PROG_INSTALL
AC_PROG_MAKE_SET

AM_ENABLE_SHARED
AM_PROG_LIBTOOL

AC_MSG_CHECKING( for debug support )
AC_ARG_ENABLE( debug,
  [  --enable-debug          enable debuging within ferite [default=no]], 
  echo "yes"; debugdefs="-DDEBUG";
  echo "**************************************************";   
  echo "* DEBUG SUPPORT IS DESIGNED TO HELP TRACK ERRORS *";
  echo "* WITHIN THE FERITE ENGINE. IT SLOWS DOWN FERITE *";
  echo "* CONSIDERABLY. CONSIDER ONLY FOR DEVELOPERS OF  *";
  echo "* THE FERITE ENGINE                              *";
  echo "**************************************************"; 
  AC_DEFINE([DEBUG], 1, [Whether we are compiling ferite in debug mode])
  ,
  echo "no";  debugdefs="")

AC_MSG_CHECKING( for x86 profiler support )
AC_ARG_ENABLE( x86-profile,
  [  --enable-x86-profile    enable x86 profiling within ferite [default=no]], 
  echo "yes"; debugdefs="$debugdefs -DUSE_X86_PROFILER",
  echo "no";)

AC_MSG_CHECKING( whether to autoload core modules )
AC_ARG_ENABLE( autoload-core,
  [  --enable-autoload-core  enable the loading of core modules automatically],
  echo "no",
  echo "yes"; debugdefs="$debugdefs -DAUTOLOAD_CORE")

AC_SUBST(debugdefs)

AC_CHECK_LIB(xml2, xmlParseDocument, 
  [gotxml=yes],
  [gotxml=no;
   exit],)

AC_ARG_WITH(libxml2,
        [  --with-libxml2=DIR         use libxml2 in <DIR>],
           PATH=$withval/bin:$PATH)

AC_CHECK_PROG(LIBXML2_CONFIG, xml2-config, xml2-config, false)

if test "$LIBXML2_CONFIG" = "false"; then
   echo "ERROR: Could not find xml2-config in your path.";
   echo "       You need libxml2 to compile Ferite.";
   echo "       Source: http://xmlsoft.org";
   exit;
   :
fi

AC_ARG_WITH(pcre,
        [  --with-pcre=DIR         use pcre in <DIR>],
           PATH=$withval/bin:$PATH)

AC_CHECK_PROG(LIBPCRE_CONFIG, pcre-config, pcre-config, false)

if test "$LIBPCRE_CONFIG" = "false"; then
   echo "ERROR: Could not find pcre-config in your path.";
   echo "       You need PCRE to compile Ferite.";
   echo "       Debian: apt-get install libpcre3 libpcre3-dev";
   echo "       Source: http://www.pcre.org";
   exit;
   :
fi

AC_MSG_CHECKING([for pcre libs])
pcre_libs=`pcre-config --libs`
AC_MSG_RESULT($pcre_libs)
AC_MSG_CHECKING([for pcre cflags])
pcre_cflags=`pcre-config --cflags`
AC_MSG_RESULT($pcre_cflags)
AC_SUBST(pcre_libs)
AC_SUBST(pcre_cflags)

thread_libs=""
thread_defs=""
for thread_lib in pthread pthread32 pthreads c_r thread dce pthreadGC2; do
    AC_CHECK_LIB($thread_lib, pthread_mutex_lock, FOUND_PTHREAD=1,FOUND_PTHREAD=0,)   
    if test "$FOUND_PTHREAD" -eq 1; then
        thread_libs="-l$thread_lib"
        thread_defs="-D_REENTRANT -DUSE_PTHREAD"
        break;
    fi
done

if test "$FOUND_PTHREAD" -ne 1; then
   echo "";
   echo "ERROR: ferite neeeds the pthread library"
   echo "";
   AC_MSG_ERROR(ERROR: pthread library not found)
fi

AC_MSG_CHECKING( for thread safety )
AC_ARG_ENABLE( thread-safe,
  [  --enable-thread-safe    enable thread safety within ferite [default=yes]], 
  [
    if test "$enableval" != "no"; then
	  thread_defs="$thread_defs -DTHREAD_SAFE"
	  AC_MSG_RESULT(yes)
	else
	  AC_MSG_RESULT(no)
	fi  
  ],
  [
    AC_MSG_RESULT(yes)
	thread_defs="$thread_defs -DTHREAD_SAFE"
  ]
)
AC_SUBST(thread_libs)
AC_SUBST(thread_defs)

gc_libs=""
gc_defs=""
AC_MSG_CHECKING( for libgc presence )
AC_ARG_ENABLE( libgc,
  [  --enable-libgc          enable use of the Boehm libgc library within ferite [default=yes]], 
  [
	AC_MSG_RESULT(no)
  ],
  [
    AC_MSG_RESULT(yes)
	AC_CHECK_LIB(gc, GC_malloc, 
		[gc_libs="-lgc"; gc_defs="-DUSE_LIBGC";
		 AC_DEFINE([HAVE_LIBGC], 1, [We can use the libgc library])],,)
  ]
)

AC_SUBST(gc_libs)
AC_SUBST(gc_defs)

sinclude(modules-config.m4)
sinclude(libs/triton/config.m4)
sinclude(libs/aphex/config.m4)

FE_LIBRARY_PATH="$prefix/lib/ferite"
AC_DEFINE_UNQUOTED([LIBRARY_DIR], "$FE_LIBRARY_PATH", [Ferites library path])
AC_SUBST(FE_LIBRARY_PATH)

FE_XPLAT_LIBRARY_PATH="$FE_LIBRARY_PATH/module-source"
AC_DEFINE_UNQUOTED(XPLAT_LIBRARY_DIR, "$FE_XPLAT_LIBRARY_PATH", [Ferites cross platform library path])
AC_SUBST(FE_XPLAT_LIBRARY_PATH)

FE_NATIVE_LIBRARY_PATH="$FE_LIBRARY_PATH/module-native"
AC_DEFINE_UNQUOTED(NATIVE_LIBRARY_DIR, "$FE_NATIVE_LIBRARY_PATH", [Ferites native library path])
AC_SUBST(FE_NATIVE_LIBRARY_PATH)

#dnl This is for builder's generated Makefile.am's 
BUILDER="\$(top_builddir)/builder/builder"
AC_SUBST(BUILDER)
MODULE_SRC_PREFIX="\$(top_srcdir)/modules"
AC_SUBST(MODULE_SRC_PREFIX)

AC_CONFIG_FILES([ferite-config \
 ferite.spec \
 Makefile \
 libs/Makefile \
 libs/aphex/Makefile \
 libs/aphex/include/Makefile \
 libs/aphex/src/Makefile \
 libs/triton/Makefile \
 libs/triton/include/Makefile \
 libs/triton/src/Makefile \
 test/Makefile \
 docs/Makefile \
 docs/images/Makefile \
 src/Makefile \
 builder/Makefile \
 include/Makefile \
 include/ferite/Makefile \
 scripts/Makefile \
 scripts/cgi/Makefile \
 scripts/test/Makefile \
 scripts/test/rmi/Makefile \
 scripts/test-suite/Makefile \
 scripts/xmlrpc/Makefile \
 tools/Makefile \
 tools/generate-module/Makefile \
 tools/generate-module/generate-module \
 tools/doc/Makefile \
 tools/doc/feritedoc \
 tools/doc/template/Makefile \
 tools/doc/template/html/Makefile \
 tools/farm/Makefile \
 tools/farm/rulesets/Makefile \
 tools/farm/farm \
 tools/manual-builder/Makefile \
 tools/manual-builder/ManualBuilder/Makefile \
 tools/manual-builder/ManualBuilder/Filters/Makefile \
 tools/manual-builder/ManualBuilder/Support/Makefile \
 tools/manual-builder/ManualBuilder/Support/Images/Makefile \
 tools/manual-builder/ManualBuilder/Support/CSS/Makefile \
 tools/manual-builder/manual-builder-html \
 tools/manual-builder/manual-builder-pdf \
 modules/Makefile \
 modules/array/Makefile \
modules/console/Makefile \
modules/date/Makefile \
modules/filesystem/Makefile \
modules/ipc/Makefile \
modules/math/Makefile \
modules/network/Makefile \
modules/number/Makefile \
modules/posix/Makefile \
modules/reflection/Makefile \
modules/regexp/Makefile \
modules/rmi/Makefile \
modules/rmi/rmi/Makefile \
modules/rmi/rmi/protocol/Makefile \
modules/rmi/rmi/translation/Makefile \
modules/rmi/rmi/transport/Makefile \
modules/serialize/Makefile \
modules/stream/Makefile \
modules/string/Makefile \
modules/sys/Makefile \
modules/test/Makefile \
modules/thread/Makefile \
modules/unix/Makefile \
modules/xml/Makefile \
])

AC_CONFIG_COMMANDS([default],[[
 chmod +x ferite-config
 chmod +x tools/doc/feritedoc
 chmod +x tools/farm/farm
 chmod +x tools/generate-module/generate-module
 chmod +x tools/manual-builder/manual-builder-html
 chmod +x tools/manual-builder/manual-builder-pdf
]],[[]])
AC_OUTPUT
