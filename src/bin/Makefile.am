all-am: common.sh
	@touch *.in

dist_bin_SCRIPTS = balancer monitor

commondir = $(libdir)
common_DATA = common.sh

edit = sed \
	-e 's|@runstatedir[@]|$(runstatedir)|g' \
	-e 's|@sysconfdir[@]|$(sysconfdir)|g'

common.sh: common.sh.in
	@$(edit) $< >$@

clean-local:
	@-rm $(common_DATA)
