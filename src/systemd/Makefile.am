AM_DISTCHECK_CONFIGURE_FLAGS = \
    --with-systemdsystemunitdir=$$dc_install_base/$(systemdsystemunitdir)

if HAVE_SYSTEMD
systemdsysconfdir = $(sysconfdir)/systemd
systemdsysconf_DATA = journald@lbw.conf
dist_systemdsystemunit_DATA = lbw-vrf.service lbw-monitor.service lbw-balancer.service

all-am: $(dist_systemdsystemunit_DATA)
	@touch *.service.in

edit = sed \
	-e 's|@libdir[@]|$(libdir)|g' \
	-e 's|@pkglibexecdir[@]|$(pkglibexecdir)|g' \
	-e 's|@runstatedir[@]|$(runstatedir)|g' \
	-e 's|@sysconfdir[@]|$(sysconfdir)|g'

$(dist_systemdsystemunit_DATA): %: %.in
	@$(edit) $< >$@

clean-local:
	@-rm -f $(dist_systemdsystemunit_DATA)
endif
