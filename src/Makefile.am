#
# libvivhook - Galcore userspace logging library
#
# Written by Wladimir J. van der Laan 2013
#

# Turn off -Wnested-externs - these are a good thing because it allows
# information hiding and helps prevent misuse of private externs.
# Turn off -Wcast-qual - this makes stuff like string assignment
# too noisy.
# Turn off -Wredundant-decls - Xorg headers seem to contain a lot
# of this, so why it's in xorg-macros.m4... maybe more of a wish?
# Turn off -Wshadow - Xorg headers seem to declare a lot of globals
# which can conflict - index, range, etc.
AM_CFLAGS = $(filter-out -Wnested-externs -Wcast-qual -Wredundant-decls \
	-Werror=write-strings -Wshadow,$(CWARNFLAGS)) \
	-std=gnu99 -Wall $(GALCORE_CFLAGS) -I$(top_srcdir)/src

libvivhook_la_LTLIBRARIES = libvivhook.la
libvivhook_ladir = $(libdir)
libvivhook_la_LDFLAGS = -version-info 1:0:0 -no-undefined 
libvivhook_la_LIBADD = $(GALCORE_LIBS)
libvivhook_la_SOURCES = \
			elf_hook.c \
			flightrecorder.cpp \
			viv_hook.c

libvivhook_la_includedir = $(includedir)/etnaviv
libvivhook_la_include_HEADERS = viv_hook.h 
