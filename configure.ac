AC_INIT([lb-wan], [1.0.0], [https://github.com/signal-09/lb-wan/issues],, [https://github.com/signal-09/lb-wan])
AM_INIT_AUTOMAKE

AC_PROG_INSTALL
PKG_PROG_PKG_CONFIG
AC_ARG_WITH([systemdsystemunitdir],
            [AS_HELP_STRING([--with-systemdsystemunitdir=DIR], [systemd])],,
            [with_systemdsystemunitdir=auto])
AS_IF([test "x$with_systemdsystemunitdir" = "xyes" -o "x$with_systemdsystemunitdir" = "xauto"],
      [def_systemdsystemunitdir=$($PKG_CONFIG --variable=systemdsystemunitdir systemd)
       AS_IF([test "x$def_systemdsystemunitdir" = "x"],
             [AS_IF([test "x$with_systemdsystemunitdir" = "xyes"],
              [AC_MSG_ERROR([systemd support requested but pkg-config unable to query systemd package])])
              with_systemdsystemunitdir=no],
              [with_systemdsystemunitdir="$def_systemdsystemunitdir"])])
AS_IF([test "x$with_systemdsystemunitdir" != "xno"],
      [AC_SUBST([systemdsystemunitdir], [$with_systemdsystemunitdir])])
AM_CONDITIONAL([HAVE_SYSTEMD], [test "x$with_systemdsystemunitdir" != "xno"])

AC_SUBST([exec_prefix], ${localstatedir}/lib/AC_PACKAGE_NAME)

AC_CONFIG_FILES([Makefile src/bin/Makefile src/etc/Makefile src/systemd/Makefile])
AC_OUTPUT
