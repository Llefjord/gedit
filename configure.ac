dnl Process this file with autoconf to produce a configure script.

AC_PREREQ(2.63.2)

m4_define(gedit_major_version, 3)
m4_define(gedit_minor_version, 14)
m4_define(gedit_micro_version, 1)
m4_define(gedit_version, gedit_major_version.gedit_minor_version.gedit_micro_version)

AC_INIT([gedit],
        [gedit_version],
	[http://bugzilla.gnome.org/enter_bug.cgi?product=gedit],
	[gedit],
	[https://wiki.gnome.org/Apps/Gedit])

# Dependencies
GLIB_REQUIRED=2.40.0
GTK_REQUIRED=3.14.0
GTKSOURCEVIEW_REQUIRED=3.14.0
LIBPEAS_REQUIRED=1.7.0
LIBXML_REQUIRED=2.5.0
ENCHANT_REQUIRED=1.2.0
ISO_CODES_REQUIRED=0.35
PYGOBJECT_REQUIRED=3.0.0

AC_CONFIG_HEADERS(config.h)
AC_CONFIG_SRCDIR(gedit/gedit.c)
AC_CONFIG_MACRO_DIR([m4])

AC_DEFINE(GEDIT_MAJOR_VERSION, gedit_major_version, [Gedit major version])
AC_SUBST(GEDIT_MAJOR_VERSION, gedit_major_version)
AC_DEFINE(GEDIT_MINOR_VERSION, gedit_minor_version, [Gedit minor version])
AC_SUBST(GEDIT_MINOR_VERSION, gedit_minor_version)
AC_DEFINE(GEDIT_MICRO_VERSION, gedit_micro_version, [Gedit micro version])
AC_SUBST(GEDIT_MICRO_VERSION, gedit_micro_version)

AM_INIT_AUTOMAKE([1.11 tar-ustar dist-xz no-dist-gzip -Wno-portability subdir-objects])
AM_MAINTAINER_MODE([enable])

AM_SILENT_RULES([yes])

# Checks for programs
AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_MAKE_SET
AC_SYS_LARGEFILE
PKG_PROG_PKG_CONFIG
# needed on osx
AC_PROG_OBJC

# Initialize libtool
LT_PREREQ([2.2.6])
LT_INIT(disable-static)

GEDIT_API_VERSION=3.12
AC_SUBST(GEDIT_API_VERSION)

GTK_DOC_CHECK([1.0],[--flavour=no-tmpl])

AC_CHECK_FUNC(sigaction)
AC_CHECK_LIB(m, floor)

dnl make sure we keep ACLOCAL_FLAGS around for maintainer builds to work
AC_SUBST(ACLOCAL_AMFLAGS, "$ACLOCAL_FLAGS -I m4")

dnl active plugins for gedit.schemas
ACTIVE_PLUGINS="'docinfo', 'modelines', 'filebrowser', 'spell', 'time'"
AC_SUBST(ACTIVE_PLUGINS)

dnl check for win32 platform
AC_MSG_CHECKING([for Win32 platform])
case "$host" in
  *-*-mingw*|*-*-cygwin*)
        archive_cmds='$CC -shared $libobjs $deplibs $compiler_flags -o $output_objdir/$soname ${wl}--enable-auto-image-base -Xlinker --output-def -Xlinker $lib.def && $DLLTOOL --dllname $soname --def $lib.def --output-delaylib $lib'
	# If the export-symbols file already is a .def file (1st line
	# is EXPORTS), use it as is; otherwise, prepend...
	archive_expsym_cmds='if test "x`$SED 1q $export_symbols`" = xEXPORTS; then
	  cp $export_symbols $output_objdir/$soname.def;
	else
	  echo EXPORTS > $output_objdir/$soname.def;
	  cat $export_symbols >> $output_objdir/$soname.def;
	fi~
	$CC -shared $output_objdir/$soname.def $libobjs $deplibs $compiler_flags -o $output_objdir/$soname ${wl}--enable-auto-image-base;
	$DLLTOOL --dllname $soname --def $output_objdir/$soname.def --output-delaylib $lib'
    platform_win32=yes
    ;;
  *)
    platform_win32=no
    ;;
esac
AC_MSG_RESULT([$platform_win32])
AM_CONDITIONAL(PLATFORM_WIN32, test "$platform_win32" = "yes")

AC_MSG_CHECKING([for native Win32])
case "$host" in
  *-*-mingw*)
    os_win32=yes
    AC_CHECK_TOOL(WINDRES, windres)
    ACTIVE_PLUGINS="${ACTIVE_PLUGINS}, 'checkupdate'"
    ;;
  *)
    os_win32=no
    ;;
esac
AC_MSG_RESULT([$os_win32])
AM_CONDITIONAL(OS_WIN32, test "$os_win32" = "yes")

dnl check for osx platform
AC_MSG_CHECKING([for Mac OS X platform])
case "$host" in
  *-*-darwin*)
    platform_osx=yes
    ;;
  *)
    platform_osx=no
    ;;
esac
AC_MSG_RESULT([$platform_osx])
AM_CONDITIONAL(PLATFORM_OSX, test "$platform_osx" = "yes")

dnl check for native osx
gdk_targets=`$PKG_CONFIG --variable=targets gdk-3.0`

gdk_windowing="x11"

for target in $gdk_targets;
do
	case "$target" in
		quartz)
			gdk_windowing=quartz
			;;
		win32)
			gdk_windowing=win32
			;;
	esac
done

AC_MSG_CHECKING([for native Mac OS X])
if test "$gdk_windowing" = "quartz"; then
	os_osx=yes
	ACTIVE_PLUGINS="${ACTIVE_PLUGINS}, 'checkupdate'"
else
	os_osx=no
fi
AC_MSG_RESULT([$os_osx])
AM_CONDITIONAL(OS_OSX, test "$os_osx" = "yes")

AM_CONDITIONAL(GDK_WINDOWING_X11, test "$gdk_windowing" = "x11")

if test "$platform_osx" = "yes"; then
	AC_DEFINE([PLATFORM_OSX],[1],[Defined if platform is Mac OSX])
fi

AC_SUBST(DEFAULT_FONT)
AC_SUBST(DEFAULT_PRINT_FONT)

if test "$os_osx" = "yes"; then
	AC_DEFINE([OS_OSX],[1],[Defined if os is Mac OSX])

	PKG_CHECK_MODULES(GTK_MAC, gtk-mac-integration)

	AC_SUBST(GTK_MAC_CFLAGS)
	AC_SUBST(GTK_MAC_LIBS)
fi

dnl ===============================================================
dnl Expanded dirs
dnl ===============================================================

# This macro expands DIR and assigns it to RET.
# If DIR is NONE, then it's replaced by DEFAULT.
# Based on AC_DEFINE_DIR
AC_DEFUN([GEDIT_FULLPATH], [
  test "x$prefix" = xNONE && prefix="$ac_default_prefix"
  test "x$exec_prefix" = xNONE && exec_prefix='${prefix}'
  ac_expand=[$]$1
  test "x$ac_expand" = xNONE && ac_expand="[$]$2"
  ac_expand=`eval echo [$]ac_expand`
  $3=`eval echo [$]ac_expand`
])

dnl FULL_LIBEXECDIR is used for X-GNOME-Bugzilla-ExtraInfoScript expansion
dnl in data/org.gnome.gedit.desktop.in.in
GEDIT_FULLPATH(libexecdir, NONE, FULL_LIBEXECDIR)
AC_SUBST(FULL_LIBEXECDIR)

dnl ================================================================
dnl Gettext stuff.
dnl ================================================================

IT_PROG_INTLTOOL([0.50.1])

AM_GNU_GETTEXT_VERSION([0.18])
AM_GNU_GETTEXT([external])

AC_SUBST([GETTEXT_PACKAGE], [gedit])
AC_DEFINE_UNQUOTED([GETTEXT_PACKAGE], "$GETTEXT_PACKAGE", [Define to the gettext package name.])

dnl ================================================================
dnl Use gvfs metadata or the old xml file store.
dnl ================================================================

if test "x$os_win32" != "xno" || test "x$os_osx" != "xno"; then
	gvfs_metadata_default="no"
else
	gvfs_metadata_default="yes"
fi

AC_ARG_ENABLE([gvfs-metadata],
	AS_HELP_STRING([--enable-gvfs-metadata], [Enable using gvfs to store metadata]),
	[enable_gvfs_metadata=$enableval],
	[enable_gvfs_metadata=$gvfs_metadata_default])

AM_CONDITIONAL(ENABLE_GVFS_METADATA, test "x$enable_gvfs_metadata" != "xno")

if test "x$enable_gvfs_metadata" != "xno"; then
	AC_DEFINE([ENABLE_GVFS_METADATA], [1], [Define to enable using gvfs metadata])
fi

dnl ================================================================
dnl check update plugin
dnl ================================================================

AC_ARG_ENABLE([updater],
	AS_HELP_STRING([--disable-updater],[Disable check update plugin (default: enabled)]),
	[enable_updater=$enableval],
	[enable_updater=yes])

if test "x$enable_updater" != "xno"; then
	PKG_CHECK_MODULES(LIBSOUP, \
		libsoup-2.4,
		have_libsoup=yes,
		have_libsoup=no)

	if test "x$have_libsoup" = "xyes"; then
		if test "x$os_win32" = "xyes"; then
			enable_updater=yes
		elif test "x$os_osx" = "xyes"; then
			enable_updater=yes
		else
			enable_updater=no
		fi
	else
		enable_updater=no
	fi
fi

AM_CONDITIONAL(ENABLE_UPDATER, test x"$enable_updater" = "xyes")

dnl ================================================================
dnl spell plugins checks: enchant and iso-codes
dnl ================================================================

AC_ARG_ENABLE([spell],
	AS_HELP_STRING([--disable-spell],[Disable spell plugin (default: enabled)]),
	[enable_enchant=$enableval],
	[enable_enchant=yes])

if test "x$enable_enchant" = "xyes" ; then

	PKG_CHECK_MODULES(ENCHANT, enchant >= $ENCHANT_REQUIRED, \
			  have_enchant=yes, have_enchant=no)

	if test "x$have_enchant" = "xyes"; then

		PKG_CHECK_EXISTS([iso-codes >= $ISO_CODES_REQUIRED],
				 [have_iso_codes=yes],[have_iso_codes=no])

		if test "x$have_iso_codes" = "xyes"; then
			AC_MSG_CHECKING([whether iso-codes has iso-639 and iso-3166 domains])
			if $PKG_CONFIG --variable=domains iso-codes | grep 639 > /dev/null && \
			   $PKG_CONFIG --variable=domains iso-codes | grep 3166 > /dev/null ; then
				result=yes
			else
				result=no
				have_iso_codes=no
			fi
			AC_MSG_RESULT([$result])
		fi

		if test "x$have_iso_codes" = "xyes"; then
			AC_DEFINE_UNQUOTED([ISO_CODES_PREFIX],["`$PKG_CONFIG --variable=prefix iso-codes`"],[ISO codes prefix])
			AC_DEFINE([HAVE_ISO_CODES],[1],[Define if you have the iso-codes package])
		else
			AC_MSG_ERROR([iso-codes is required to build the spell plugin. Use --disable-spell to build without spell plugin.])
		fi
	else
		enable_enchant=no
		AC_MSG_ERROR([Enchant library not found or too old. Use --disable-spell to build without spell plugin.])
	fi
fi

AM_CONDITIONAL(ENABLE_ENCHANT, test x"$enable_enchant" = "xyes")

AC_ARG_ENABLE([python],
              AS_HELP_STRING([--enable-python[=@<:@no/auto/yes@:>@]],[Build with python support]),
              [enable_python=$enableval],
              [enable_python="auto"])

if test "x$enable_python" = "xauto"; then
	PKG_CHECK_EXISTS([pygobject-3.0 >= $PYGOBJECT_REQUIRED],
	                 [enable_python=yes],[enable_python=no])
fi

if test "x$enable_python" = "xyes"; then
	AM_PATH_PYTHON(3.2.3)
	PKG_CHECK_MODULES(PYTHON, [pygobject-3.0 >= $PYGOBJECT_REQUIRED])

	pyoverridesdir="\$(pyexecdir)/gi/overrides"
	AC_SUBST(pyoverridesdir)
fi

AM_CONDITIONAL(ENABLE_PYTHON, test x"$enable_python" = "xyes")

dnl ================================================================
dnl Start of pkg-config checks
dnl ================================================================

# Dependencies

PKG_CHECK_MODULES(GEDIT, [
	libxml-2.0 >= $LIBXML_REQUIRED
	glib-2.0 >= $GLIB_REQUIRED
	gio-2.0 >= $GLIB_REQUIRED
	gmodule-2.0
	gtk+-3.0 >= $GTK_REQUIRED
	gtksourceview-3.0 >= $GTKSOURCEVIEW_REQUIRED
	libpeas-1.0 >= $LIBPEAS_REQUIRED
	libpeas-gtk-1.0 >= $LIBPEAS_REQUIRED
	gsettings-desktop-schemas
])

if test "$os_osx" = "no" &&
   test "$os_win32" = "no"; then
	PKG_CHECK_MODULES(X11, [
		x11
	])
else
	X11_CFLAGS=
	X11_LIBS=
fi

if test "$platform_win32" = "no"; then
	PKG_CHECK_MODULES(UNIX, [
		gio-unix-2.0 >= 2.25.5
	])
else
	UNIX_CFLAGS=
	UNIX_LIBS=
fi

GEDIT_CFLAGS="$GEDIT_CFLAGS $X11_CFLAGS $UNIX_CFLAGS"
GEDIT_LIBS="$GEDIT_LIBS $X11_LIBS $UNIX_LIBS"

AC_SUBST(GEDIT_CFLAGS)
AC_SUBST(GEDIT_LIBS)

dnl ================================================================
dnl libgd
dnl ================================================================

LIBGD_INIT([tagged-entry static])

dnl ================================================================
dnl GSettings stuff
dnl ================================================================

GLIB_GSETTINGS

dnl ================================================================
dnl Misc
dnl ================================================================
AC_PATH_PROG(GLIB_COMPILE_RESOURCES, glib-compile-resources)
AC_PATH_PROG(GLIB_GENMARSHAL, glib-genmarshal)
AC_PATH_PROG(GLIB_MKENUMS, glib-mkenums)

AC_DEFINE(GDK_VERSION_MIN_REQUIRED, GDK_VERSION_3_8, [minimum gdk version])

GNOME_COMPILE_WARNINGS([maximum])

AC_ARG_ENABLE(deprecations,
              [AS_HELP_STRING([--enable-deprecations],
                              [warn about deprecated usages [default=no]])],
              [enable_deprecations=$enableval], [])

AC_MSG_CHECKING([whether to forbid deprecated symbols])
if test "$enable_deprecations" = "yes"; then
	AC_MSG_RESULT(yes)
	DISABLE_DEPRECATED_CFLAGS="\
-DGSEAL_ENABLE \
-DG_DISABLE_DEPRECATED \
-DGDK_DISABLE_DEPRECATED \
-DGTK_DISABLE_DEPRECATED \
-DGDK_PIXBUF_DISABLE_DEPRECATED \
-DGNOME_DISABLE_DEPRECATED \
-DPEAS_DISABLE_DEPRECATED"

	AC_SUBST(DISABLE_DEPRECATED_CFLAGS)
else
	AC_MSG_RESULT(no)
fi

# on win32 plugins must be able to resolve all symbols at link time
# so we create a lib
if test "$platform_win32" = yes; then
	GEDIT_IMPLIB='-L${libdir}/gedit -lgedit'
	PLUGIN_LIBTOOL_FLAGS="-module -avoid-version -no-undefined"
	if test "$os_win32" = yes; then
		PLUGIN_LIBTOOL_FLAGS="${PLUGIN_LIBTOOL_FLAGS} -mwindows"
	fi
else
	GEDIT_IMPLIB=""
	PLUGIN_LIBTOOL_FLAGS="-module -avoid-version"
fi
AC_SUBST(GEDIT_IMPLIB)
AC_SUBST(PLUGIN_LIBTOOL_FLAGS)

GEDIT_PLUGINS_DATA_DIR="$datadir/gedit/plugins"
AC_SUBST(GEDIT_PLUGINS_DATA_DIR)

GEDIT_PLUGINS_LIBS_DIR="$libdir/gedit/plugins"
AC_SUBST(GEDIT_PLUGINS_LIBS_DIR)

YELP_HELP_INIT

# Introspection
GOBJECT_INTROSPECTION_CHECK([0.9.3])

if test "$found_introspection" = "yes"; then
	enable_introspection=yes
	AC_DEFINE([ENABLE_INTROSPECTION], [1], [Define to enable GObject Introspection])
else
	enable_introspection=no
fi

# Vala
VAPIGEN_CHECK([0.25.1])

if test "$os_osx" = "yes"; then
	os_menus_ui=menus-osx.ui
	os_style_css=gedit-style-osx.css
else
	os_menus_ui=menus-default.ui
	os_style_css=

	OS_DEPENDENT_RESOURCE_FILES='<file preprocess="xml-stripblanks">gtk/menus-traditional.ui</file>'
fi

if test "x$os_style_css" != "x"; then
	OS_DEPENDENT_RESOURCE_FILES+="<file>css/gedit-style-os.css</file>"
	AC_CONFIG_LINKS(gedit/resources/css/gedit-style-os.css:gedit/resources/css/$os_style_css)
fi

AC_SUBST(OS_DEPENDENT_RESOURCE_FILES)
AC_CONFIG_LINKS(gedit/resources/gtk/menus.ui:gedit/resources/gtk/$os_menus_ui)

AC_CONFIG_FILES([
Makefile
gedit/resources/gedit.gresource.xml
data/org.gnome.gedit.desktop.in
data/gedit.pc
data/org.gnome.gedit.gschema.xml
docs/Makefile
docs/reference/Makefile
help/Makefile
libgd/Makefile
plugins/checkupdate/org.gnome.gedit.plugins.checkupdate.gschema.xml.in
plugins/externaltools/org.gnome.gedit.plugins.externaltools.gschema.xml.in
plugins/filebrowser/org.gnome.gedit.plugins.filebrowser.gschema.xml.in
plugins/pythonconsole/org.gnome.gedit.plugins.pythonconsole.gschema.xml.in
plugins/time/org.gnome.gedit.plugins.time.gschema.xml.in
po/Makefile.in
osx/bundle/data/Info.plist])

AC_OUTPUT

echo "

Configuration:

	Source code location:	${srcdir}
	Compiler:		${CC}
	Spell Plugin enabled:	$enable_enchant
	Gvfs metadata enabled:	$enable_gvfs_metadata
	Deprecations enabled:	$enable_deprecations
	GObject Introspection:	$enable_introspection
	GDK Backend:            $gdk_windowing
	Python support:         $enable_python
	Vala support:           $enable_vala
"

if expr ${GEDIT_MINOR_VERSION} % 2 > /dev/null; then
	stable_version=`expr ${GEDIT_MINOR_VERSION} - 1`

cat << EOF
 *** IMPORTANT ***

This is an unstable version of gedit.
It is for test purposes only.

Please, DO NOT use it in a production environment.
It will probably crash and you will lose your data.

If you are looking for a stable release of gedit, either download
it from:

  http://ftp.gnome.org/pub/gnome/sources/gedit/${GEDIT_MAJOR_VERSION}.${stable_version}/

Or checkout the gnome-${GEDIT_MAJOR_VERSION}-${stable_version} branch of the gedit module from:

  git://git.gnome.org/gedit

Thanks,
The gedit team

 *** END OF WARNING ***
EOF

fi
