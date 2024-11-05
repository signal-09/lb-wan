AM_DISTCHECK_CONFIGURE_FLAGS = \
    --with-systemdsystemunitdir=$$dc_install_base/$(systemdsystemunitdir)

if HAVE_SYSTEMD
dist_systemdsystemunit_DATA = lbw-vrf.service lbw-monitor.service lbw-balancer.service

all-am: $(dist_systemdsystemunit_DATA)
	@touch *.service.in

$(dist_systemdsystemunit_DATA): %: %.in
	@sed -e 's|@bindir[@]|$(bindir)|g' $< >$@

clean-local:
	@-rm -f $(dist_systemdsystemunit_DATA)
endif
