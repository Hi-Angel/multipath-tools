include ../Makefile.inc

SONAME = 0
DEVLIB = libmpathpersist.so
LIBS = $(DEVLIB).$(SONAME)
VERSION_SCRIPT:= libmpathpersist.version

CFLAGS += $(LIB_CFLAGS) -I$(multipathdir) -I$(mpathutildir) -I$(mpathpersistdir) -I$(mpathcmddir)
LDFLAGS += -L$(multipathdir) -L$(mpathutildir) -L$(mpathcmddir)

LIBDEPS += -lmultipath -lmpathutil -lmpathcmd -ldevmapper -lpthread -ldl

OBJS = mpath_persist.o mpath_updatepr.o mpath_pr_ioctl.o mpath_persist_int.o

all: $(DEVLIB)

$(LIBS): $(OBJS) $(VERSION_SCRIPT)
	$(CC) $(LDFLAGS) $(SHARED_FLAGS) -Wl,-soname=$@ \
		-Wl,--version-script=$(VERSION_SCRIPT) -o $@ $(OBJS) $(LIBDEPS)

$(NV_VERSION_SCRIPT):	$(VERSION_SCRIPT)
	@printf 'NOVERSION {\nglobal:\n' >$@
	@grep -P '^[ \t]+[a-zA-Z_][a-zA-Z0-9_]*;' $< >>$@
	@printf 'local:\n\t*;\n};\n' >>$@

$(LIBS:%.so.$(SONAME)=%-nv.so):	$(OBJS) $(NV_VERSION_SCRIPT)
	$(CC) $(LDFLAGS) $(SHARED_FLAGS) -Wl,-soname=$@ \
		-Wl,--version-script=$(NV_VERSION_SCRIPT) -o $@ $(OBJS) $(LIBDEPS)

abi:    $(LIBS:%.so.$(SONAME)=%-nv.abi)

$(DEVLIB): $(LIBS)
	$(LN) $(LIBS) $@

install: all
	$(INSTALL_PROGRAM) -d $(DESTDIR)$(syslibdir)
	$(INSTALL_PROGRAM) -m 755 $(LIBS) $(DESTDIR)$(syslibdir)/$(LIBS)
	$(INSTALL_PROGRAM) -m 755 -d $(DESTDIR)$(syslibdir)
	$(INSTALL_PROGRAM) -m 755 -d $(DESTDIR)$(man3dir)
	$(INSTALL_PROGRAM) -m 755 -d $(DESTDIR)$(includedir)
	$(LN) $(LIBS) $(DESTDIR)$(syslibdir)/$(DEVLIB)
	$(INSTALL_PROGRAM) -m 644 mpath_persistent_reserve_in.3 $(DESTDIR)$(man3dir)
	$(INSTALL_PROGRAM) -m 644 mpath_persistent_reserve_out.3 $(DESTDIR)$(man3dir)
	$(INSTALL_PROGRAM) -m 644 mpath_persist.h $(DESTDIR)$(includedir)

uninstall:
	$(RM) $(DESTDIR)$(syslibdir)/$(LIBS)
	$(RM) $(DESTDIR)$(man3dir)/mpath_persistent_reserve_in.3
	$(RM) $(DESTDIR)$(man3dir)/mpath_persistent_reserve_out.3
	$(RM) $(DESTDIR)$(includedir)/mpath_persist.h
	$(RM) $(DESTDIR)$(syslibdir)/$(DEVLIB)

clean: dep_clean
	$(RM) core *.a *.o *.so *.so.* *.abi $(NV_VERSION_SCRIPT)

include $(wildcard $(OBJS:.o=.d))


dep_clean:
	$(RM) $(OBJS:.o=.d)
