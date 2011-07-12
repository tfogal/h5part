# Every configure script must call AC_INIT before doing anything else.
# AC_INIT (package, version, [bug-report], [tarname])
AC_INIT([H5Part], [1.6.2], [h5part@lists.psi.ch], H5Part)
AC_PREREQ(2.60)

AC_USE_SYSTEM_EXTENSIONS

# should be called right after AC_INIT.
# configure scripts can create a C header file containing `#define' directives. 
# The AC_CONFIG_HEADERS macro selects this kind of output.
AC_CONFIG_HEADER(config.h)

AC_CONFIG_MACRO_DIR([m4])
LT_INIT([shared pic-only])

###############################################################################
################# --enable-xxx and --with-xxx Argument ########################
###############################################################################

AC_ARG_ENABLE(
	[debug],
	[AC_HELP_STRING([--enable-debug],
	[Compile with debug support [default=no]])],
	[USE_DEBUG=$enableval])

AC_ARG_ENABLE(
	[shared],
	[AC_HELP_STRING([--enable-shared],
	[Compile with shared library support [default=no]])],
	[USE_SHARED=$enableval])

AC_ARG_ENABLE(
	[64],
	[AC_HELP_STRING([--enable-64],
	[Compile using 64-bit flags [default=no]])],
	[USE_64=$enableval])

AC_ARG_ENABLE(
	[fortran],
        [AC_HELP_STRING([--enable-fortran],
	[Compile the Fortran interface [default=no]])],
        [USE_FORTRAN=$enableval])
AM_CONDITIONAL(ENABLE_FORTRAN, test "x${USE_FORTRAN}" = "xyes")

AC_ARG_ENABLE(
	[parallel],
        [AC_HELP_STRING([--enable-parallel],
	[Compile the MPI/IO interface [default=no]])],
        [USE_PARALLEL=$enableval])
AC_DEFINE_UNQUOTED(PARALLEL_IO, test "x${USE_PARALLEL}" = "xyes",
                   Add in support for parallel IO features.)

AC_ARG_ENABLE(
	[tools],
        [AC_HELP_STRING([--enable-tools],
	[Compile h5part tools [default=no]])],
        [USE_TOOLS=$enableval])

dnl AC_ARG_WITH(
dnl 	[mpipath],
dnl 	[AC_HELP_STRING([--with-mpipath],
dnl 	[path to MPI installation [default=""]])],
dnl         [MPIPATH=$withval], [MPIPATH=""])

AC_ARG_WITH(
  [hdf5],
  [AS_HELP_STRING([--with-hdf5], [path to hdf5 (default /usr)])],
  [use_hdf5=${withval}],
  [use_hdf5=default]
)

###############################################################################
############### PATH SERACH FUNCTION - to be used later... ####################
###############################################################################
# /*@@
#   @routine    CCTK_Search
#   @date       Wed Jul 21 11:16:35 1999
#   @author     Tom Goodale
#   @desc
#   Used to search for something in various directories
#   @enddesc
#@@*/

PATH_Search()
{
  eval  $1=""
  if test $# -lt 4 ; then
    h5part_basedir=""
  else
    h5part_basedir="$4/"
  fi
  for h5part_place in $2
    do
      echo -n "looking in $h5part_place ... $ac_c" #1>&6
      if test -r "$h5part_basedir$h5part_place/$3" ; then
        echo "$ac_t"" found" #1>&6
        eval $1="$h5part_place"
        break
      fi
      if test -d "$h5part_basedir$h5part_place/$3" ; then
        echo "$ac_t"" found" #1>&6
        eval $1="$h5part_place"
        break
      fi
      echo "$ac_t"" no" #1>&6
    done

  return
}



###############################################################################
############# MISC SETTINGS INCLUDING C & C++ COMPILER SETTING ################
###############################################################################
# Compute the canonical host-system type variable, host, and its three
# individual parts host_cpu, host_vendor, and host_os.
AC_CANONICAL_HOST


uname=`uname -s`
if test $uname = "AIX"; then
  AC_MSG_CHECKING([if system is AIX])
  AC_MSG_RESULT([OK])

  # If on AIX, define _ALL_SOURCE. Allows the use of some BSD functions. 
  # Should be called before any macros that run the C compiler.
  AC_AIX
fi


# DAVINCI SPECIFIC!!!
if test `uname -n` = "davinci"; then
   echo "DAVINCI SPECIFIC TESTING FOR STDC++ LIBRARY!"
   PATH_Search STDCXX_CHECK '/usr/lib64 /usr/lib' libstdc++.a

   # if STDCXX_CHECK is set...
   if test "$STDCXX_CHECK" = "/usr/lib64"; then
      echo "STDCXX setting ..."
      echo "STDCXX_CHECK = $STDCXX_CHECK ..."
      STDCXX="-L/usr/lib64 -lstdc++"
      echo "STDCXX =  $STDCXX ..."
   elif test "$STDCXX_CHECK" = "/usr/lib"; then
      echo "STDCXX setting ..."
      echo "STDCXX_CHECK = $STDCXX_CHECK ..."
      STDCXX="-L/usr/lib -lstdc++"
      echo "STDCXX =  $STDCXX ..."
   fi 
fi


# Determine a C compiler to use. 
# If CC is not already set in the environment, check for gcc and cc, then
# for other C compilers. 
# Set output variable CC to the name of the compiler found.
if test -z "$CC" && test -n "$MPICC"; then
  CC="$MPICC"
fi
AC_PROG_CC([cc gcc pgcc pathcc icc cc_r])
AX_MPI([CC="$MPICC"; LIBS="$MPILIBS $LIBS"],
       [AC_MSG_ERROR([MPI required!])])

# AC_PROG_CC doesn't pick up cc_r in Bassi. The following AC_PATH_PROGS
# is the fix.
AC_PATH_PROGS([BAS_CC], [cc_r], [], [$PATH])

# if BAS_CC not empty
if test -n "$BAS_CC"; then
  echo "CC setting for Bassi ..."
  CC=$BAS_CC
  echo "CC =  $CC ..."
fi 


# Determine a C++ compiler to use. 
# Check if the environment variable CXX or CCC (in that order) is set; 
# if so, then set output variable CXX to its value.
# Otherwise, if the macro is invoked without an argument, 
# then search for a C++ compiler under the likely names (first g++ and c++
# then other names). 
# If none of those checks succeed, then as a last resort set CXX to g++.
AC_PROG_CXX(pgCC pathCC icc cc_r g++ gcc cc)


# Set output variable INSTALL to the path of a BSD-compatible install program,
# if one is found in the current PATH. 
# Otherwise, set INSTALL to `dir/install-sh -c`
AC_PROG_INSTALL


# AM_INIT_AUTOMAKE is required to use autoconf with automake
AM_INIT_AUTOMAKE()

AC_PROG_LIBTOOL

# Default prefix for bindir, etc... (eg >> ./build/bin)
AC_PREFIX_DEFAULT(`pwd`/build)


# AC_DEFINE_UNQUOTED (variable, value, [description])
# Define the C preprocessor variable variable to value
# Use this macro instead of AC_DEFINE when variable or value is a shell variable. 
AC_DEFINE_UNQUOTED(MY_BUILD_VENDOR, "$host_vendor", "")
AC_DEFINE_UNQUOTED(MY_BUILD_CPU, "$host_cpu", "")
AC_DEFINE_UNQUOTED(MY_BUILD_OS, "$host_os", "")
AC_DEFINE_UNQUOTED(MY_GNUNAME, "${host_cpu}-${host_vendor}-${host_os}", "")
AC_DEFINE_UNQUOTED(MY_UNAME, "$uname", "")



###############################################################################
######################## CONFIGURE LINE OPTIONS ###############################
###############################################################################

AC_MSG_CHECKING([if debug is enabled])

if test "X$USE_DEBUG" = "Xyes"; then
    AC_MSG_RESULT([yes])
    CFLAGS="$CFLAGS -g"
else
    AC_MSG_RESULT([no])
fi

# shared library
AC_MSG_CHECKING([if shared libraries are enabled])

if test "X$USE_SHARED" = "Xyes"; then
    AC_MSG_RESULT([yes])
    CFLAGS="$CFLAGS -fPIC"
    FFLAGS="$FFLAGS -fPIC"
else
    AC_MSG_RESULT([no])
fi

###################### 64-bit compilation enabled #############################
AC_MSG_CHECKING([if 64-bit compilation is enabled])


# If --enable-64 is set in the configure line
if test "X$USE_64:/f90
" = "Xyes"; then
    AC_MSG_RESULT([yes])
    if test $uname = "AIX"; then
        CFLAGS="$CFLAGS -q64"
        FFLAGS="$FFLAGS -q64"
    fi

    if test $uname = "IRIX64"; then
        CFLAGS="$CFLAGS -64"
        FFLAGS="$FFLAGS -64 -fPIC -fno-second-underscore"
    fi
else
    AC_MSG_RESULT([no])
fi


############################ fortran enabled ##################################
AC_MSG_CHECKING([if fortran interface enabled])
if test "X$USE_FORTRAN" = "Xyes"; then
    AC_MSG_RESULT([yes])

    AC_PROG_FC(pgf90 ifort xlf_r pathf90 g95 g90 ftn gfortran)
    if test -z "$FC" ; then
        AC_MSG_ERROR([Cannot find a fortran compiler!!!])
        exit 1
    fi

    if test ! $uname = "AIX"; then
        FFLAGS="${FFLAGS} -fPIC"
    fi

    if test $FC = "g90"; then
	FFLAGS="${FFLAGS} -fno-second-underscore"
    fi

    if test $FC = "g95"; then
	FFLAGS="${FFLAGS} -fno-second-underscore"
    fi

    if test $FC = "gfortran"; then
        FFLAGS="${FFLAGS} -DHAVE_GFORTRAN"
    fi

    AC_MSG_CHECKING([symbol convention in object files])
    `cd src && rm -f TestUnderscore.o TestUnderscoreC.o TestUnderscore`
    `cd src && ${FC} ${FFLAGS} -c TestUnderscore.f`
    `cd src && ${CC} ${CFLAGS} -c TestUnderscoreC.c`
    `cd src && ${FC} ${FFLAGS} -o TestUnderscore TestUnderscore.o TestUnderscoreC.o -lc`

    if test -f src/TestUnderscore ; then
        UNDERSCORE_H=Underscore.h
        `cd src && ./TestUnderscore > Underscore.h`
        AC_MSG_RESULT([ok])
    else
        AC_MSG_RESULT([nok])
        AC_MSG_ERROR([Cannot build fortran executables!!!])
        exit 1
    fi
else
    AC_MSG_RESULT([no])
fi

AC_MSG_CHECKING([whether tools are enabled])
if test "X$USE_TOOLS" = "Xyes"; then
    AC_MSG_RESULT([yes])
    BUILD_TOOLS="h5pAttrib h5pToGNUplot homdynToH5p"
    # tools + parallel
    if test "X$USE_PARALLEL" = "Xyes"; then
        BUILD_TOOLS="${BUILD_TOOLS} H5PartBench H5BlockBench"
    fi
else
    AC_MSG_RESULT([no])
fi

###############################################################################
######################### PATH CHECKING & SETTING #############################
###############################################################################

dnl if they gave us a path to HDF5, use it.
# We assume that the given hdf5 directory has 'include' and 'lib'
# subdirectories.
AS_IF([test "x${use_hdf5}" != xdefault],
  [CFLAGS="-I${use_hdf5}/include ${CFLAGS}"
   LDFLAGS="-L${use_hdf5}/lib"]
)

AC_CHECK_LIB([hdf5], [H5open],
  [AC_SUBST([LIBHDF5], "-lhdf5")
   AC_DEFINE([HAVE_LIBHDF5], [1], [Define if you have HDF5])
   LIBS="-lhdf5 ${LIBS}"],
  [AC_MSG_FAILURE([HDF5 is *required* for H5Part!])],
)

AC_MSG_CHECKING([for static zlib root ])
echo
PATH_Search ZLIBROOT '/apps/zlib' lib/libz.a
if test -n "$ZLIBROOT"; then
    LDFLAGS="$LDFLAGS -L$ZLIBROOT/lib"
fi

FCFLAGS=${FFLAGS}
CXXFLAGS=${CFLAGS}


###############################################################################
#################### MISC SETTINGS - path, flags, etc #########################
###############################################################################

H5P_LIB_LOC=`pwd`/src

###############################################################################
################# A SIMPLE WORK AROUND TO USE ENV. VARS #######################
###############################################################################

###############################################################################
############## EXPORTING VARIABLES & CREATING OUTPUT FILES ####################
###############################################################################
# AC_SUBST (variable, [value])
# Create an output variable from a shell variable. 
# Make AC_OUTPUT substitute the variable variable into output files (typically one or more `Makefile's). 
# This means that AC_OUTPUT will replace instances of `@variable@' in input files with the value that 
# the shell variable variable has when AC_OUTPUT is called. 
# This value of variable should not contain literal newlines.
# If value is given, in addition assign it to variable. 
AC_SUBST(MPIROOT)
AC_SUBST(HDF5ROOT)
AC_SUBST(MPIINC)
AC_SUBST(MPILIB)
AC_SUBST(MPICC)
AC_SUBST(MPICXX)
AC_SUBST(MPIFC)
AC_SUBST(MTARGET)
AC_SUBST(TTARGET)
AC_SUBST(SZLIB)
AC_SUBST(CFLAGS)
AC_SUBST(FFLAGS)
AC_SUBST(STDCXX)
AC_SUBST(H5P_LIB_LOC)
AC_SUBST(H5P_LIB_NAME)
AC_SUBST(UNDERSCORE_H)
AC_SUBST(BUILD_TOOLS)
AC_SUBST(LDFLAGS)

# Make AC_OUTPUT create each `file' by copying an input file (by default `file.in'),
# substituting the output variable values.
AC_CONFIG_FILES([
Makefile
doc/Makefile
src/Makefile
test/Makefile
tools/Makefile
])

AC_OUTPUT

###############################################################################
########################## PRINTING SUMMARY ###################################
###############################################################################
echo
echo
echo "Summary for `(hostname || uname -n) 2>/dev/null | sed 1q`:"
echo
echo "Host OS:             $host_os"
echo "Host CPU:            $host_cpu"
echo "Host vendor:         $host_vendor"
echo "Build libraries:     $MTARGET"
echo "Build test programs: $TTARGET"
echo "Build tools:         $BUILD_TOOLS"
echo "CC =                 $CC"
echo "CXX =                $CXX"
echo "FC =                 $FC"
echo "MPICC =              $MPICC"
echo "MPICXX =             $MPICXX"
echo "MPIFC =              $MPIFC"
echo "CFLAGS =             $CFLAGS"
echo "FFLAGS =             $FFLAGS"
echo "MPILIB =             $MPILIB"
echo "MPIINC =             $MPIINC"
echo "MPIROOT =            $MPIROOT"
echo "HDF5ROOT =           $HDF5ROOT"
echo "LDFLAGS =            $LDFLAGS"
echo
