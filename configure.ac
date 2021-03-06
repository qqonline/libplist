#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ(2.61)
AC_INIT(libplist, 1.11, nospam@nowhere.com)
AM_INIT_AUTOMAKE([dist-bzip2 no-dist-gzip])
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES])
AC_CONFIG_SRCDIR([src/])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])

dnl libtool versioning
# +1 : 0 : +1  == adds new functions to the interface
# +1 : 0 : 0   == changes or removes functions (changes include both
#                 changes to the signature and the semantic)
#  ? :+1 : ?   == just internal changes
# CURRENT : REVISION : AGE
LIBPLIST_SO_VERSION=2:0:0

AC_SUBST(LIBPLIST_SO_VERSION)

dnl Minimum package versions
LIBXML2_VERSION=2.8.0

AC_SUBST(LIBXML2_VERSION)

# Checks for programs.
AC_PROG_CC
AC_PROG_CXX
AM_PROG_CC_C_O
AC_PROG_LIBTOOL

# Checks for libraries.
PKG_CHECK_MODULES(libxml2, libxml-2.0 >= $LIBXML2_VERSION)

# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([stdint.h stdlib.h string.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_SIZE_T
AC_TYPE_SSIZE_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_UINT8_T

# Checks for library functions.
AC_CHECK_FUNCS([asprintf strcasecmp strdup strerror strndup stpcpy vasprintf])

AC_CHECK_HEADER(endian.h, [ac_cv_have_endian_h="yes"], [ac_cv_have_endian_h="no"])
if test "x$ac_cv_have_endian_h" = "xno"; then
  AC_DEFINE(__LITTLE_ENDIAN,1234,[little endian])
  AC_DEFINE(__BIG_ENDIAN,4321,[big endian])
  AC_C_BIGENDIAN([ac_cv_c_bigendian="yes"], [ac_cv_c_bigendian="no"], [], [])
  if test "x$ac_cv_c_bigendian" = "xyes"; then
    AC_DEFINE(__BYTE_ORDER,4321,[big endian byte order])
  else
    AC_DEFINE(__BYTE_ORDER,1234,[little endian byte order])
  fi
fi

# Check for operating system
AC_MSG_CHECKING([whether to enable WIN32 build settings])
case ${host_os} in
  *mingw32*|*cygwin*)
    win32=true
    AC_MSG_RESULT([yes])
    AC_SUBST(WINDRES)
    ;;
  *)
    win32=false
    AC_MSG_RESULT([no])
    ;;
esac
AM_CONDITIONAL(WIN32, test x$win32 = xtrue)

# Cython Python Bindings
AC_ARG_WITH([cython],
            [AS_HELP_STRING([--without-cython],
            [build Python bindings using Cython (default is yes)])],
            [build_cython=false],
            [build_cython=true])
if test "$build_cython" = "true"; then
            AM_PATH_PYTHON(2.3)
            AC_PROG_CYTHON(0.17.0)
            CYTHON_PYTHON
else
            CYTHON=false
fi
if [test "x$CYTHON" != "xfalse"]; then
            AC_MSG_CHECKING([for libplist Cython bindings])
            CYTHON_PLIST_INCLUDE_DIR=$($PKG_CONFIG --variable=includedir libplist)/plist/cython
            if [test ! -d "$CYTHON_PLIST_INCLUDE_DIR"]; then
                CYTHON=false
                CYTHON_SUB=
                cython_python_bindings=no
                AC_MSG_RESULT([no])
                AC_MSG_WARN([cannot find libplist Cython bindings. You should install your distribution specific libplist Cython bindings package.])
            else
                AC_SUBST([CYTHON_PLIST_INCLUDE_DIR])
                AC_MSG_RESULT([$CYTHON_PLIST_INCLUDE_DIR])
                CYTHON_SUB=cython
                cython_python_bindings=yes
            fi
else
            CYTHON_SUB=
            cython_python_bindings=no
fi
AM_CONDITIONAL([HAVE_CYTHON],[test "x$CYTHON_SUB" = "xcython"])
AC_SUBST([CYTHON_SUB])

AS_COMPILER_FLAGS(GLOBAL_CFLAGS, "-Wall -Wextra -Wmissing-declarations -Wredundant-decls -Wshadow -Wpointer-arith  -Wwrite-strings -Wswitch-default -Wno-unused-parameter")
AC_SUBST(GLOBAL_CFLAGS)

m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([yes])])

AC_OUTPUT([
Makefile
libcnary/Makefile
src/Makefile
include/Makefile
tools/Makefile
cython/Makefile
test/Makefile
libplist.pc
libplist++.pc
doxygen.cfg
])

echo "
Configuration for $PACKAGE $VERSION:
-------------------------------------------

  Install prefix: .........: $prefix
  Python bindings .........: $cython_python_bindings

  Now type 'make' to build $PACKAGE $VERSION,
  and then 'make install' for installation.
"
