# Makefile for ACPI daemon

# update these numbers for new releases
VERSION = 1.0.10

INSTPREFIX =
BINDIR = $(INSTPREFIX)/usr/bin
SBINDIR = $(INSTPREFIX)/usr/sbin
MAN8DIR = $(INSTPREFIX)/usr/share/man/man8

SBIN_PROGS = acpid
BIN_PROGS = acpi_listen
PROGS = $(SBIN_PROGS) $(BIN_PROGS)

acpid_SRCS = acpid.c event.c ud_socket.c
acpid_OBJS = $(acpid_SRCS:.c=.o)

acpi_listen_SRCS = acpi_listen.c ud_socket.c
acpi_listen_OBJS = $(acpi_listen_SRCS:.c=.o)

all_SRCS = $(acpid_SRCS) $(acpi_listen_SRCS)

MAN8 = acpid.8 acpi_listen.8
MAN8GZ = $(MAN8:.8=.8.gz)

CFLAGS = -W -Wall -Werror -Wundef -Wshadow -D_GNU_SOURCE -O2 -g $(DEFS)
DEFS = -DVERSION="\"$(VERSION)\""

all: $(PROGS)

acpid: $(acpid_OBJS)

acpi_listen: $(acpi_listen_OBJS)

man: $(MAN8)
	for a in $^; do gzip -f -9 -c $$a > $$a.gz; done

install: $(PROGS) man
	mkdir -p $(SBINDIR)
	mkdir -p $(BINDIR)
	install -m 750 acpid $(SBINDIR)
	install -m 755 acpi_listen $(BINDIR)
	mkdir -p $(MAN8DIR)
	install -m 644 $(MAN8GZ) $(MAN8DIR)

DISTTMP=/tmp
dist:
	rm -rf $(DISTTMP)/acpid-$(VERSION)
	mkdir -p $(DISTTMP)/acpid-$(VERSION)
	cp -a * $(DISTTMP)/acpid-$(VERSION)
	find $(DISTTMP)/acpid-$(VERSION) -type d -name CVS | xargs rm -rf
	make -C $(DISTTMP)/acpid-$(VERSION) clean
	tar -C $(DISTTMP) -zcvf acpid-$(VERSION).tar.gz acpid-$(VERSION)
	rm -rf $(DISTTMP)/acpid-$(VERSION)

clean:
	$(RM) $(PROGS) $(MAN8GZ) *.o .depend

dep depend:
	@$(RM) .depend
	@$(MAKE) .depend

.depend: $(all_SRCS)
	@for f in $^; do \
		OBJ=$$(echo $$f | sed 's/\.cp*$$/.o/'); \
		$(CPP) $(PP_INCLUDES) -MM $$f -MT $$OBJ; \
	done > $@

# NOTE: 'sinclude' is "silent-include".  This suppresses a warning if
# .depend does not exist.  Since Makefile includes this file, and this
# file includes .depend, .depend is itself "a makefile" and Makefile is
# dependent on it.  Any makefile for which there is a rule (as above for
# .depend) will be evaluated before anything else.  If the rule executes
# and the makefile is updated, make will reload the original Makefile and
# start over.
#
# This means that the .depend rule will always be checked first.  If
# .depend gets rebuilt, then the dependencies we have already sincluded
# must have been stale.  Make starts over, the old dependencies are
# tossed, and the new dependencies are sincluded.
#
# So why use 'sinclude' instead of 'include'?  We want to ALWAYS make
# Makefile depend on .depend, even if .depend doesn't exist yet.  But we
# don't want that pesky warning.
sinclude .depend
