AUTOMAKE_OPTIONS = foreign subdir-objects

EXTRA_DIST = src/etc/lb-wan.conf \
             AUTHORS
#            README \
#            INSTALL \
#            NEWS \
#            TODO \
#            ChangeLog

SUBDIRS = src/bin src/etc src/systemd

distclean-local:
	-rm depcomp install-sh missing
	-rm Makefile.in src/bin/Makefile.in src/systemd/Makefile.in src/etc/Makefile.in
	-rm aclocal.m4 configure
	-rm -r $(PACKAGE_NAME)-$(PACKAGE_VERSION)
	-rm *~
