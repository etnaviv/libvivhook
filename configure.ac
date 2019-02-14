AC_PREREQ([2.63])
AC_INIT([libvivhook], [1.0.0], [], [libvivhook])

AC_CONFIG_SRCDIR([Makefile.am])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_AUX_DIR([build-aux])

AM_INIT_AUTOMAKE([1.10 foreign dist-bzip2])
AM_MAINTAINER_MODE([enable])

# Enable quiet compiles on automake 1.11.
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

# Check for programs
AC_PROG_CXX
AC_PROG_CC

AC_USE_SYSTEM_EXTENSIONS

# Initialize libtool
LT_PREREQ([2.2])
LT_INIT([disable-static])

AC_CHECK_FUNCS([clock_gettime], [CLOCK_LIB=],
               [AC_CHECK_LIB([rt], [clock_gettime], [CLOCK_LIB=-lrt],
                             [AC_MSG_ERROR([Could not find clock_gettime])])])
AC_SUBST([CLOCK_LIB])

AC_ARG_WITH(galcore-include,
            AC_HELP_STRING([--with-galcore-include=PATH],
                           [specify directory for installed galcore include files [[default=/usr/local/include/HAL]]]),
            [galcore_include="$withval"],
            [galcore_include="/usr/local/include/HAL"])

AC_ARG_WITH(galcore-lib,
            AC_HELP_STRING([--with-galcore-lib=PATH],
                           [specify directory for installed libGAL library [[default=/usr/local/lib]]]),
            [galcore_lib="$withval"],
            [galcore_lib="/usr/local/lib"])

GALCORE_CFLAGS="-I$galcore_include"
GALCORE_LIBS="-L$galcore_lib -lGAL"

saved_CPPFLAGS="$CPPFLAGS"
CPPFLAGS="$CPPFLAGS $GALCORE_CFLAGS"
AC_CHECK_HEADERS([gc_hal.h gc_hal_base.h],
                 [],
                 [AC_MSG_ERROR([required libGAL header file missing])])
AC_CHECK_HEADERS([gc_hal_driver.h gc_hal_kernel_buffer.h],
                 [],
                 [AC_MSG_ERROR([required libGAL header file missing])],
                 [#include <gc_hal.h>])
AC_CHECK_HEADERS([gc_abi.h],
                 [],
                 [AC_MSG_ERROR([required galcore_headers file missing])])
CPPFLAGS="$saved_CPPFLAGS"

saved_LDFLAGS="$LDFLAGS"
LDFLAGS="$LDFLAGS $GALCORE_LIBS"
AC_CHECK_LIB([GAL],[gco3D_WriteBuffer],
             [],
             [AC_MSG_ERROR([required libGAL missing])])
LDFLAGS="$saved_LDFLAGS"

AC_SUBST([GALCORE_CFLAGS])
AC_SUBST([GALCORE_LIBS])

pkgconfigdir=${libdir}/pkgconfig
AC_SUBST(pkgconfigdir)

AC_CONFIG_FILES([
	Makefile
	src/Makefile
	libvivhook.pc
])
AC_OUTPUT
