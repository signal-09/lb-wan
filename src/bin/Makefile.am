pkglibexec_SCRIPTS = lbw lbw-vrf lbw-monitor lbw-balancer

commondir = $(pkglibexecdir)
common_DATA = common.sh

all-am: $(common_DATA)
	@touch *.in

edit = sed \
	-e 's|@libdir[@]|$(libdir)|g' \
	-e 's|@pkglibexecdir[@]|$(pkglibexecdir)|g' \
	-e 's|@runstatedir[@]|$(runstatedir)|g' \
	-e 's|@sysconfdir[@]|$(sysconfdir)|g'

$(common_DATA): %: %.in
	@$(edit) $< >$@

install-exec-hook:
	ln -sf $(DETDIR)$(pkglibexecdir)/lbw $(DESTDIR)$(sbindir)/lbw

clean-local:
	@-rm -f $(common_DATA)
