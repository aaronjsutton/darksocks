VERSION_INFO = 2:0:0

AM_CFLAGS = -g -O2 -Wall -Werror -Wno-deprecated-declarations -fno-strict-aliasing -std=gnu99 -D_GNU_SOURCE
AM_CFLAGS += $(PTHREAD_CFLAGS)
if !USE_SYSTEM_SHARED_LIB
AM_CFLAGS += -I$(top_srcdir)/libbloom
AM_CFLAGS += -I$(top_srcdir)/libipset/include
AM_CFLAGS += -I$(top_srcdir)/libcork/include
endif
AM_CFLAGS += $(LIBPCRE_CFLAGS)

SS_COMMON_LIBS = $(INET_NTOP_LIB) $(LIBPCRE_LIBS)
if !USE_SYSTEM_SHARED_LIB
SS_COMMON_LIBS += $(top_builddir)/libbloom/libbloom.la \
                  $(top_builddir)/libipset/libipset.la \
                  $(top_builddir)/libcork/libcork.la
else
SS_COMMON_LIBS += -lbloom -lcork -lcorkipset
endif
SS_COMMON_LIBS += -lev -lsodium -lm

bin_PROGRAMS = ds-local ds-tunnel ds-server ds-manager

sni_src = http.c \
          tls.c

acl_src = rule.c \
          acl.c

crypto_src = crypto.c \
             aead.c \
             stream.c \
			 ppbloom.c \
             base64.c

plugin_src = plugin.c

ds_local_SOURCES = utils.c \
                   jconf.c \
                   json.c \
                   udprelay.c \
                   cache.c \
                   netutils.c \
                   local.c \
                   $(crypto_src) \
                   $(plugin_src) \
                   $(sni_src) \
                   $(acl_src)

ds_tunnel_SOURCES = utils.c \
                    jconf.c \
                    json.c \
                    udprelay.c \
                    cache.c \
                    netutils.c \
                    tunnel.c \
                    $(crypto_src) \
                    $(plugin_src)

ds_server_SOURCES = utils.c \
                    netutils.c \
                    jconf.c \
                    json.c \
                    udprelay.c \
                    cache.c \
                    resolv.c \
                    server.c \
                    $(crypto_src) \
                    $(plugin_src) \
                    $(sni_src) \
                    ${acl_src}

ds_manager_SOURCES = utils.c \
                     jconf.c \
                     json.c \
                     netutils.c \
                     manager.c

ds_local_LDADD = $(SS_COMMON_LIBS)
ds_tunnel_LDADD = $(SS_COMMON_LIBS)
ds_server_LDADD = $(SS_COMMON_LIBS)
ds_manager_LDADD = $(SS_COMMON_LIBS)
ds_local_LDADD += -lcares
ds_tunnel_LDADD += -lcares
ds_server_LDADD += -lcares
ds_manager_LDADD += -lcares

ds_local_CFLAGS = $(AM_CFLAGS) -DMODULE_LOCAL
ds_tunnel_CFLAGS = $(AM_CFLAGS) -DMODULE_TUNNEL
ds_server_CFLAGS = $(AM_CFLAGS) -DMODULE_REMOTE
ds_manager_CFLAGS = $(AM_CFLAGS) -DMODULE_MANAGER

if BUILD_REDIRECTOR
bin_SCRIPTS = ss-nat
bin_PROGRAMS += ss-redir
ss_redir_SOURCES = utils.c \
                   jconf.c \
                   json.c \
                   netutils.c \
                   cache.c \
                   udprelay.c \
                   redir.c \
                   $(crypto_src) \
                   $(plugin_src)

ss_redir_CFLAGS = $(AM_CFLAGS) -DMODULE_REDIR
ss_redir_LDADD = $(SS_COMMON_LIBS)
ss_redir_LDADD += -lcares
endif

lib_LTLIBRARIES = libdarksocks.la
libdarksocks_la_SOURCES = $(ds_local_SOURCES)
libdarksocks_la_CFLAGS = $(ds_local_CFLAGS) -DLIB_ONLY
libdarksocks_la_LDFLAGS = -version-info $(VERSION_INFO)
libdarksocks_la_LIBADD = $(ds_local_LDADD)
include_HEADERS = darksocks.h

noinst_HEADERS = acl.h crypto.h stream.h aead.h json.h netutils.h redir.h server.h tls.h uthash.h \
                 cache.h http.h local.h plugin.h resolv.h tunnel.h utils.h base64.h ppbloom.h \
                 common.h jconf.h manager.h protocol.h rule.h socks5.h udprelay.h
EXTRA_DIST = ss-nat
