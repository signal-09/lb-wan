AM_DISTCHECK_CONFIGURE_FLAGS = \
    --with-systemdsystemunitdir=$$dc_install_base/$(systemdsystemunitdir)

if HAVE_SYSTEMD
dist_systemdsystemunit_DATA = wan-monitor.service lb-wan.service

all-am: $(dist_systemdsystemunit_DATA)
	@touch *.service.in

wan-monitor.service: wan-monitor.service.in
	@sed -e 's|@bindir[@]|$(bindir)|g' $< >$@

lb-wan.service: lb-wan.service.in
	@sed -e 's|@bindir[@]|$(bindir)|g' $< >$@

clean-local:
	@-rm $(dist_systemdsystemunit_DATA)
endif
