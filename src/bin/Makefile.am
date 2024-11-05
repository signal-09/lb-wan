all-am: common.sh
	@touch *.in

dist_bin_SCRIPTS = lbw-vrf lbw-monitor lbw-balancer

commondir = $(libdir)
common_DATA = common.sh

edit = sed \
	-e 's|@runstatedir[@]|$(runstatedir)|g' \
	-e 's|@sysconfdir[@]|$(sysconfdir)|g'

common.sh: common.sh.in
	@$(edit) $< >$@

clean-local:
	@-rm -f $(common_DATA)
