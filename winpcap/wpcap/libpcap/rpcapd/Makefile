################################
# Makefile for the remote daemon
################################
include ../../../../vars.mk

CFLAGS		= -DHAVE_REMOTE -DHAVE_SNPRINTF -D_GNU_SOURCE=1 -O3 -g
LINUXCFLAGS = -pthread
OSXCFLAGS   = -pthread -mmacosx-version-min=10.6 -DHAVE_VSNPRINTF -DHAVE_STRLCPY \
              -DUSE_DISPATCHED_FOR_IFRECV
WINCFLAGS   = -DHAVE_STRUCT_TIMESPEC

#flags for debugging: -D_DEBUG -g -Wall

INCLUDE	= -I../
WININCLUDE = -I../Win32/Include -I../../../Common -I../../../../win32-pthreads

LINUXLIB = -lpcap -lcrypt -static
OSXLIB   = -lpcap
WINLIB   = -lwpcap -lpthreadGC2 -lpacket -lws2_32

LINUXLIBPATH = -L../
WINLIBPATH   = -L../../lib -L../../../../win32-pthreads \
			   -L../../../packetNtx/Dll/Project

# Files that are needed to compile this project
FILES = rpcapd.c daemon.c utils.c fileconf.c ../pcap-remote.c ../sockutils.c \
		../pcap-new.c
WINFILES = win32-svc.c win32-messages.o

LINUXTARGET = rpcapd
OSXTARGET   = rpcapd.osx  # XXX: should be just rpcapd
WINTARGET   = rpcapd.exe

# XXX: this is awful...I think this is why they invented autotools 25 years ago.
ifeq ($(PLATFORM), windows)
	CC = $(WINCC)
	FILES += $(WINFILES)
	CFLAGS += $(WINCFLAGS)
	INCLUDE += $(WININCLUDE)
	LIB += $(WINLIB)
	LIBPATH += $(WINLIBPATH)
	TARGET = $(WINTARGET)
else ifeq ($(PLATFORM), linux)
	CC = $(LINUXCC)
	FILES += $(LINUXFILES)
	CFLAGS += $(LINUXCFLAGS)
	INCLUDE += $(LINUXINCLUDE)
	LIB += $(LINUXLIB)
	LIBPATH += $(LINUXLIBPATH)
	TARGET = $(LINUXTARGET)
else ifeq ($(PLATFORM), osx)
	CC = $(OSXCC)
	FILES += $(OSXFILES)
	CFLAGS += $(OSXCFLAGS)
	INCLUDE += $(OSXINCLUDE)
	LIB += $(OSXLIB)
	LIBPATH += $(OSXLIBPATH)
	TARGET = $(OSXTARGET)
else
default:
	$(MAKE) PLATFORM=linux executable
ifdef WINCC_VERSION
	$(MAKE) PLATFORM=windows executable
endif
endif

executable: $(TARGET)

# XXX: using object files would make incremental rebuilds faster...
$(TARGET): $(FILES)
	$(CC) $(CFLAGS) $(INCLUDE) $(LIBPATH) -o $(TARGET) $(FILES) $(LIB)

win32-messages.o: win32-messages.mc
	x86_64-w64-mingw32-windmc $<
	x86_64-w64-mingw32-windres -i win32-messages.rc -o $@

install:
	cp $(LINUXTARGET) rpcapd-dist/rpcapd-64bit-linux
	chmod 0755 rpcapd-dist/rpcapd-64bit-linux

clean:
	rm -f rpcapd rpcapd.osx rpcapd.exe win32-messages.bin win32-messages.h \
		  win32-messages.o win32-messages.rc

distclean: clean
	rm -f rpcapd-dist/rpcapd-64bit-linux
