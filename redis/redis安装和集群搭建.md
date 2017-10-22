# redis 安装和集群搭建

### 1.下载镜像

```
$ wget http://download.redis.io/releases/redis-3.2.3.tar.gz
```

### 2.解压压缩包

```
$ tar xzf redis-3.2.3.tar.gz
$ cd redis-3.2.3
```

### 3.编译下载包

```
$ make
```

编译显示结果如下

```
 cd src && make all
 make[1]: Entering directory `/root/redis/src'
 rm -rf redis-server redis-sentinel redis-cli redis-benchmark redis-check-rdb redis-check-aof *.o *.gcda *.gcno *.gcov redis.info lcov-html
 (cd ../deps && make distclean)
 make[2]: Entering directory `/root/redis/deps'
 (cd hiredis && make clean) > /dev/null || true
 (cd linenoise && make clean) > /dev/null || true
 (cd lua && make clean) > /dev/null || true
 (cd geohash-int && make clean) > /dev/null || true
 (cd jemalloc && [ -f Makefile ] && make distclean) > /dev/null || true
 (rm -f .make-*)
 make[2]: Leaving directory `/root/redis/deps'
 (rm -f .make-*)
 echo STD=-std=c99 -pedantic -DREDIS_STATIC='' >> .make-settings
 echo WARN=-Wall -W >> .make-settings
 echo OPT=-O2 >> .make-settings
 echo MALLOC=jemalloc >> .make-settings
 echo CFLAGS= >> .make-settings
 echo LDFLAGS= >> .make-settings
 echo REDIS_CFLAGS= >> .make-settings
 echo REDIS_LDFLAGS= >> .make-settings
 echo PREV_FINAL_CFLAGS=-std=c99 -pedantic -DREDIS_STATIC='' -Wall -W -O2 -g -ggdb -I../deps/geohash-int -I../deps/hiredis -I../deps/linenoise -I../deps/lua/src -DUSE_JEMALLOC -I../deps/jemalloc/include >> .make-settings
 echo PREV_FINAL_LDFLAGS= -g -ggdb -rdynamic >> .make-settings
 (cd ../deps && make hiredis linenoise lua geohash-int jemalloc)
 make[2]: Entering directory `/root/redis/deps'
 (cd hiredis && make clean) > /dev/null || true
 (cd linenoise && make clean) > /dev/null || true
 (cd lua && make clean) > /dev/null || true
 (cd geohash-int && make clean) > /dev/null || true
 (cd jemalloc && [ -f Makefile ] && make distclean) > /dev/null || true
 (rm -f .make-*)
 (echo "" > .make-ldflags)
 (echo "" > .make-cflags)
 MAKE hiredis
 cd hiredis && make static
 make[3]: Entering directory `/root/redis/deps/hiredis'
 cc -std=c99 -pedantic -c -O3 -fPIC -Wall -W -Wstrict-prototypes -Wwrite-strings -g -ggdb net.c
 cc -std=c99 -pedantic -c -O3 -fPIC -Wall -W -Wstrict-prototypes -Wwrite-strings -g -ggdb hiredis.c
 cc -std=c99 -pedantic -c -O3 -fPIC -Wall -W -Wstrict-prototypes -Wwrite-strings -g -ggdb sds.c
 cc -std=c99 -pedantic -c -O3 -fPIC -Wall -W -Wstrict-prototypes -Wwrite-strings -g -ggdb async.c
 ar rcs libhiredis.a net.o hiredis.o sds.o async.o
 make[3]: Leaving directory `/root/redis/deps/hiredis'
 MAKE linenoise
 cd linenoise && make
 make[3]: Entering directory `/root/redis/deps/linenoise'
 cc -Wall -Os -g -c linenoise.c
 make[3]: Leaving directory `/root/redis/deps/linenoise'
 MAKE lua
 cd lua/src && make all CFLAGS="-O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' " MYLDFLAGS="" AR="ar rcu"
 make[3]: Entering directory `/root/redis/deps/lua/src'
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lapi.o lapi.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lcode.o lcode.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o ldebug.o ldebug.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o ldo.o ldo.c
 ldo.c: In function ‘f_parser’:
 ldo.c:496: warning: unused variable ‘c’
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o ldump.o ldump.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lfunc.o lfunc.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lgc.o lgc.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o llex.o llex.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lmem.o lmem.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lobject.o lobject.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lopcodes.o lopcodes.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lparser.o lparser.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lstate.o lstate.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lstring.o lstring.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o ltable.o ltable.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o ltm.o ltm.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lundump.o lundump.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lvm.o lvm.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lzio.o lzio.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o strbuf.o strbuf.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o fpconv.o fpconv.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lauxlib.o lauxlib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lbaselib.o lbaselib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o ldblib.o ldblib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o liolib.o liolib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lmathlib.o lmathlib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o loslib.o loslib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o ltablib.o ltablib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lstrlib.o lstrlib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o loadlib.o loadlib.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o linit.o linit.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lua_cjson.o lua_cjson.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lua_struct.o lua_struct.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lua_cmsgpack.o lua_cmsgpack.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lua_bit.o lua_bit.c
 ar rcu liblua.a lapi.o lcode.o ldebug.o ldo.o ldump.o lfunc.o lgc.o llex.o lmem.o lobject.o lopcodes.o lparser.o lstate.o lstring.o ltable.o ltm.o lundump.o lvm.o lzio.o strbuf.o fpconv.o lauxlib.o lbaselib.o ldblib.o liolib.o lmathlib.o loslib.o ltablib.o lstrlib.o loadlib.o linit.o lua_cjson.o lua_struct.o lua_cmsgpack.o lua_bit.o # DLL needs all object files
 ranlib liblua.a
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o lua.o lua.c
 cc -o lua lua.o liblua.a -lm
 liblua.a(loslib.o): In function `os_tmpname':
 loslib.c:(.text+0x35): warning: the use of `tmpnam' is dangerous, better use `mkstemp'
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o luac.o luac.c
 cc -O2 -Wall -DLUA_ANSI -DENABLE_CJSON_GLOBAL -DREDIS_STATIC='' -c -o print.o print.c
 cc -o luac luac.o print.o liblua.a -lm
 make[3]: Leaving directory `/root/redis/deps/lua/src'
 MAKE geohash-int
 cd geohash-int && make
 make[3]: Entering directory `/root/redis/deps/geohash-int'
 cc -Wall -O2 -g -c geohash.c
 cc -Wall -O2 -g -c geohash_helper.c
 make[3]: Leaving directory `/root/redis/deps/geohash-int'
 MAKE jemalloc
 cd jemalloc && ./configure --with-lg-quantum=3 --with-jemalloc-prefix=je_ --enable-cc-silence CFLAGS="-std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops " LDFLAGS=""
 checking for xsltproc... /usr/bin/xsltproc
 checking for gcc... gcc
 checking whether the C compiler works... yes
 checking for C compiler default output file name... a.out
 checking for suffix of executables...
 checking whether we are cross compiling... no
 checking for suffix of object files... o
 checking whether we are using the GNU C compiler... yes
 checking whether gcc accepts -g... yes
 checking for gcc option to accept ISO C89... none needed
 checking how to run the C preprocessor... gcc -E
 checking for grep that handles long lines and -e... /bin/grep
 checking for egrep... /bin/grep -E
 checking for ANSI C header files... yes
 checking for sys/types.h... yes
 checking for sys/stat.h... yes
 checking for stdlib.h... yes
 checking for string.h... yes
 checking for memory.h... yes
 checking for strings.h... yes
 checking for inttypes.h... yes
 checking for stdint.h... yes
 checking for unistd.h... yes
 checking whether byte ordering is bigendian... no
 checking size of void *... 8
 checking size of int... 4
 checking size of long... 8
 checking size of intmax_t... 8
 checking build system type... x86_64-unknown-linux-gnu
 checking host system type... x86_64-unknown-linux-gnu
 checking whether pause instruction is compilable... yes
 checking for ar... ar
 checking malloc.h usability... yes
 checking malloc.h presence... yes
 checking for malloc.h... yes
 checking whether malloc_usable_size definition can use const argument... no
 checking whether __attribute__ syntax is compilable... yes
 checking whether compiler supports -fvisibility=hidden... yes
 checking whether compiler supports -Werror... yes
 checking whether tls_model attribute is compilable... yes
 checking whether compiler supports -Werror... yes
 checking whether alloc_size attribute is compilable... yes
 checking whether compiler supports -Werror... yes
 checking whether format(gnu_printf, ...) attribute is compilable... yes
 checking whether compiler supports -Werror... yes
 checking whether format(printf, ...) attribute is compilable... yes
 checking for a BSD-compatible install... /usr/bin/install -c
 checking for ranlib... ranlib
 checking for ld... /usr/bin/ld
 checking for autoconf... false
 checking for memalign... yes
 checking for valloc... yes
 checking configured backtracing method... N/A
 checking for sbrk... yes
 checking whether utrace(2) is compilable... no
 checking whether valgrind is compilable... no
 checking whether a program using __builtin_ffsl is compilable... yes
 checking LG_PAGE... 12
 checking pthread.h usability... yes
 checking pthread.h presence... yes
 checking for pthread.h... yes
 checking for pthread_create in -lpthread... yes
 checking for library containing clock_gettime... -lrt
 checking for secure_getenv... no
 checking for issetugid... no
 checking for _malloc_thread_cleanup... no
 checking for _pthread_mutex_init_calloc_cb... no
 checking for TLS... yes
 checking whether C11 atomics is compilable... no
 checking whether atomic(9) is compilable... no
 checking whether Darwin OSAtomic*() is compilable... no
 checking whether madvise(2) is compilable... yes
 checking whether to force 32-bit __sync_{add,sub}_and_fetch()... no
 checking whether to force 64-bit __sync_{add,sub}_and_fetch()... no
 checking for __builtin_clz... yes
 checking whether Darwin OSSpin*() is compilable... no
 checking whether glibc malloc hook is compilable... yes
 checking whether glibc memalign hook is compilable... yes
 checking whether pthreads adaptive mutexes is compilable... yes
 checking for stdbool.h that conforms to C99... yes
 checking for _Bool... yes
 configure: creating ./config.status
 config.status: creating Makefile
 config.status: creating jemalloc.pc
 config.status: creating doc/html.xsl
 config.status: creating doc/manpages.xsl
 config.status: creating doc/jemalloc.xml
 config.status: creating include/jemalloc/jemalloc_macros.h
 config.status: creating include/jemalloc/jemalloc_protos.h
 config.status: creating include/jemalloc/jemalloc_typedefs.h
 config.status: creating include/jemalloc/internal/jemalloc_internal.h
 config.status: creating test/test.sh
 config.status: creating test/include/test/jemalloc_test.h
 config.status: creating config.stamp
 config.status: creating bin/jemalloc-config
 config.status: creating bin/jemalloc.sh
 config.status: creating bin/jeprof
 config.status: creating include/jemalloc/jemalloc_defs.h
 config.status: creating include/jemalloc/internal/jemalloc_internal_defs.h
 config.status: creating test/include/test/jemalloc_test_defs.h
 config.status: executing include/jemalloc/internal/private_namespace.h commands
 config.status: executing include/jemalloc/internal/private_unnamespace.h commands
 config.status: executing include/jemalloc/internal/public_symbols.txt commands
 config.status: executing include/jemalloc/internal/public_namespace.h commands
 config.status: executing include/jemalloc/internal/public_unnamespace.h commands
 config.status: executing include/jemalloc/internal/size_classes.h commands
 config.status: executing include/jemalloc/jemalloc_protos_jet.h commands
 config.status: executing include/jemalloc/jemalloc_rename.h commands
 config.status: executing include/jemalloc/jemalloc_mangle.h commands
 config.status: executing include/jemalloc/jemalloc_mangle_jet.h commands
 config.status: executing include/jemalloc/jemalloc.h commands
 ===============================================================================
 jemalloc version : 4.0.3-0-ge9192eacf8935e29fc62fddc2701f7942b1cc02c
 library revision : 2

 CONFIG : --with-lg-quantum=3 --with-jemalloc-prefix=je_ --enable-cc-silence 'CFLAGS=-std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops ' LDFLAGS=
 CC : gcc
 CFLAGS : -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -fvisibility=hidden
 CPPFLAGS : -D_GNU_SOURCE -D_REENTRANT
 LDFLAGS :
 EXTRA_LDFLAGS :
 LIBS : -lpthread
 TESTLIBS : -lrt
 RPATH_EXTRA :

 XSLTPROC : /usr/bin/xsltproc
 XSLROOT :

 PREFIX : /usr/local
 BINDIR : /usr/local/bin
 DATADIR : /usr/local/share
 INCLUDEDIR : /usr/local/include
 LIBDIR : /usr/local/lib
 MANDIR : /usr/local/share/man

 srcroot :
 abs_srcroot : /root/redis/deps/jemalloc/
 objroot :
 abs_objroot : /root/redis/deps/jemalloc/

 JEMALLOC_PREFIX : je_
 JEMALLOC_PRIVATE_NAMESPACE
  : je_
 install_suffix :
 autogen : 0
 cc-silence : 1
 debug : 0
 code-coverage : 0
 stats : 1
 prof : 0
 prof-libunwind : 0
 prof-libgcc : 0
 prof-gcc : 0
 tcache : 1
 fill : 1
 utrace : 0
 valgrind : 0
 xmalloc : 0
 munmap : 0
 lazy_lock : 0
 tls : 1
 cache-oblivious : 1
 ===============================================================================
 cd jemalloc && make CFLAGS="-std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops " LDFLAGS="" lib/libjemalloc.a
 make[3]: Entering directory `/root/redis/deps/jemalloc'
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/jemalloc.o src/jemalloc.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/arena.o src/arena.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/atomic.o src/atomic.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/base.o src/base.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/bitmap.o src/bitmap.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/chunk.o src/chunk.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/chunk_dss.o src/chunk_dss.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/chunk_mmap.o src/chunk_mmap.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/ckh.o src/ckh.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/ctl.o src/ctl.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/extent.o src/extent.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/hash.o src/hash.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/huge.o src/huge.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/mb.o src/mb.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/mutex.o src/mutex.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/pages.o src/pages.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/prof.o src/prof.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/quarantine.o src/quarantine.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/rtree.o src/rtree.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/stats.o src/stats.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/tcache.o src/tcache.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/util.o src/util.c
 gcc -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops -c -D_GNU_SOURCE -D_REENTRANT -Iinclude -Iinclude -o src/tsd.o src/tsd.c
 ar crus lib/libjemalloc.a src/jemalloc.o src/arena.o src/atomic.o src/base.o src/bitmap.o src/chunk.o src/chunk_dss.o src/chunk_mmap.o src/ckh.o src/ctl.o src/extent.o src/hash.o src/huge.o src/mb.o src/mutex.o src/pages.o src/prof.o src/quarantine.o src/rtree.o src/stats.o src/tcache.o src/util.o src/tsd.o
 make[3]: Leaving directory `/root/redis/deps/jemalloc'
 make[2]: Leaving directory `/root/redis/deps'
  CC adlist.o
  CC quicklist.o
  CC ae.o
 In file included from ae.c:53:
 ae_epoll.c: In function ‘aeApiAddEvent’:
 ae_epoll.c:75: warning: missing initializer
 ae_epoll.c:75: warning: (near initialization for ‘ee.data’)
 ae_epoll.c: In function ‘aeApiDelEvent’:
 ae_epoll.c:92: warning: missing initializer
 ae_epoll.c:92: warning: (near initialization for ‘ee.data’)
  CC anet.o
 anet.c: In function ‘anetSockName’:
 anet.c:640: warning: dereferencing pointer ‘s’ does break strict-aliasing rules
 anet.c:638: note: initialized from here
 anet.c:644: warning: dereferencing pointer ‘s’ does break strict-aliasing rules
 anet.c:642: note: initialized from here
 anet.c: In function ‘anetPeerToString’:
 anet.c:584: warning: dereferencing pointer ‘s’ does break strict-aliasing rules
 anet.c:582: note: initialized from here
 anet.c:588: warning: dereferencing pointer ‘s’ does break strict-aliasing rules
 anet.c:586: note: initialized from here
 anet.c: In function ‘anetTcpAccept’:
 anet.c:555: warning: dereferencing pointer ‘s’ does break strict-aliasing rules
 anet.c:553: note: initialized from here
 anet.c:559: warning: dereferencing pointer ‘s’ does break strict-aliasing rules
 anet.c:557: note: initialized from here
  CC dict.o
  CC server.o
  CC sds.o
  CC zmalloc.o
  CC lzf_c.o
  CC lzf_d.o
  CC pqsort.o
  CC zipmap.o
  CC sha1.o
  CC ziplist.o
  CC release.o
  CC networking.o
  CC util.o
  CC object.o
  CC db.o
  CC replication.o
  CC rdb.o
  CC t_string.o
  CC t_list.o
  CC t_set.o
  CC t_zset.o
  CC t_hash.o
  CC config.o
  CC aof.o
  CC pubsub.o
  CC multi.o
  CC debug.o
  CC sort.o
  CC intset.o
  CC syncio.o
  CC cluster.o
  CC crc16.o
  CC endianconv.o
  CC slowlog.o
  CC scripting.o
  CC bio.o
  CC rio.o
  CC rand.o
  CC memtest.o
  CC crc64.o
  CC bitops.o
  CC sentinel.o
  CC notify.o
  CC setproctitle.o
  CC blocked.o
  CC hyperloglog.o
  CC latency.o
  CC sparkline.o
  CC redis-check-rdb.o
  CC geo.o
  LINK redis-server
  INSTALL redis-sentinel
  CC redis-cli.o
  LINK redis-cli
  CC redis-benchmark.o
  LINK redis-benchmark
  INSTALL redis-check-rdb
  CC redis-check-aof.o
  LINK redis-check-aof

 Hint: It's a good idea to run 'make test' ;)
```

修改配置参数

```
$ echo 511
$ echo "vm.overcommit_memory=1" > /etc/sysctl.conf
$ sysctl vm.overcommit_memory=1
```
### 4.编译成功后启动redis服务器

```
$ ./src/redis-server redis.conf
```

后台启动方式

加上`&`号可以使redis以后台程序方式运行

```
 ./redis-server &
```

或者修改redis.conf中daemonize no为yes

然后
redis-server redis.conf

### 5.启动redis客户端

```
$ src/redis-cli
$ src/redis-cli -h 192.168.1.103 -p 6379  
```

### 6.设置访问密码

Redis 数据库可以配置安全保护的，所以任何客户端在连接执行命令时需要进行身份验证。为了确保 Redis 的安全，需要在配置文件设置密码。

下面给出的例子显示的步骤是用来确保 Redis 实例的安全。

```
127.0.0.1:6379> CONFIG get requirepass
 1) "requirepass"
 2) ""
```

默认情况下此属性是空的，这意味着此实例没有设置密码。可以通过执行以下命令来修改设置此属性

```
127.0.0.1:6379> CONFIG set requirepass "yiibaipass"
 OK
 127.0.0.1:6379> CONFIG get requirepass
 1) "requirepass"
 2) "yiibaipass"
```

如果客户端运行命令无需验证设置密码，那么（错误）NOAUTH 需要验证。错误将返回。因此，客户端需要使用 AUTH 命令来验证自己的身份信息。

AUTH命令的基本语法如下所示：

```
127.0.0.1:6379> AUTH password
```

## 集群搭建

### redis-trib管理器

Redis作者应该是个Ruby爱好者，Ruby客户端就是他开发的。这次集群的管理功能没有嵌入到Redis代码中，于是作者又顺手写了个叫做redis-trib的管理脚本。redis-trib依赖Ruby和RubyGems，以及redis扩展。可以先用which命令查看是否已安装ruby和rubygems，用gem list –local查看本地是否已安装redis扩展。

最简便的方法就是用apt或yum包管理器安装RubyGems后执行gem install redis。如果网络或环境受限的话，可以手动安装RubyGems和redis扩展（国外链接可能无法下载，可以从CSDN下载）：

### 1.安装ruby，因为用来执行redis-trib.rb。

源码安装

下载 Ruby 之后，解压到新创建的目录下：

```
$ tar -xvzf ruby-2.2.3.tgz
$ cd ruby-2.2.3
```

现在，配置并编译源代码，如下所示：

```
$ ./configure
$ make
$ sudo make install
```

安装后，通过在命令行中输入以下命令来确保一切工作正常：

```
$ruby -v
ruby 2.2.3……
```

### 2.安装rubygems，因为要在gem命令下安装redis

```
 # tar -zxvf rubygems-0.9.0.tgz
 # cd rubygems-0.9.0
 # ruby setup.rb
```

检查是否安装成功

```
 # gem -v
```

### 3.安装zlib包

```
tar -xzvf zlib-1.2.8.tar.gz
cd zlib-1.2.8
./configure --prefix=/opt/zlib
make
make install
```

### 4.安装ruby-zlib

```
 cd ruby-2.1.6/ext/zlib
 ruby ./extconf.rb --with-zlib-dir=/opt/zlib
 make
 make install
```

### 5.安装redis-3.2.1.gem

```
gem install -l redis-3.2.1.gem
```
准备集群redis相关配置文件

要让集群正常工作至少需要3个主节点，在这里我们要创建6个redis节点，

其中三个为主节点，三个为从节点，对应的redis节点的ip和端口对应关系如下：

```
127.0.0.1:7000
127.0.0.1:7001
127.0.0.1:7002
127.0.0.1:7003
127.0.0.1:7004
127.0.0.1:7005
```

### 1、redis集群配置：

将redis.conf复制一份作为redis-cluster.conf，修改如下配置：

```
port 6379
 bing #建议将主机地址127.0.0.1和主机的实际IP或局域网地址都绑定
 appendonly yes
 daemonize yes #允许以后台程序允许
跟集群有关的配置：
cluster-enabled yes
 cluster-config-file nodes-6379.conf #（建议以nodes-端口号的形式命名，方便辨识）
 cluster-node-timeout 15000
 cluster-slave-validity-factor 10
 cluster-migration-barrier 1
 cluster-require-full-coverage yes
```

redis-cluster.conf复制六份到以端口号命名的文件夹下，并将名称更改为端口号以便于区分。

分别修改如下配置：

```
port #对应端口号

 bing #对应ip 建议加上127.0.0.1原因后面解释

 cluster-config-file
```

然后根据不同的配置文件进行启动

```
./src/redis-server redis-cluster_7000/redis-cluster.conf
./src/redis-server redis-cluster_7001/redis-cluster.conf
./src/redis-server redis-cluster_7002/redis-cluster.conf
./src/redis-server redis-cluster_7003/redis-cluster.conf
./src/redis-server redis-cluster_7004/redis-cluster.conf
./src/redis-server redis-cluster_7005/redis-cluster.conf
```

然后观察一下进程，看是否启动

```
[root@server01 redis]# ps -ef | grep redis
 root 17150 1 0 22:39 ? 00:00:01 ./src/redis-server 10.211.55.15:7000 [cluster]
 root 17183 1 0 22:39 ? 00:00:01 ./src/redis-server 10.211.55.15:7001 [cluster]
 root 17210 1 0 22:39 ? 00:00:01 ./src/redis-server 10.211.55.15:7002 [cluster]
 root 17243 1 0 22:40 ? 00:00:01 ./src/redis-server 10.211.55.15:7003 [cluster]
 root 17277 1 0 22:40 ? 00:00:01 ./src/redis-server 10.211.55.15:7004 [cluster]
 root 17293 1 0 22:40 ? 00:00:01 ./src/redis-server 10.211.55.15:7005 [cluster]
```
集群启动和建立

然后就可以进入redis的src目录执行

这里的--replicas 1 表示每个主节点下有一个从节点

```
./src/redis-trib.rb create --replicas 1 10.211.55.15:7000 10.211.55.15:7001 10.211.55.15:7002 10.211.55.15:7003 10.211.55.15:7004 10.211.55.15:7005 10.211.55.15:7006
```

默认是前三个为主，后三个为从

默认情况下不能从slaves读取数据，但建立连接后，执行一次命令READONLY，该slaves即可读取数据。

客户端登录

用redis-cli -c -h -p命令登录
```
-c是以集群方式登录；
-h后跟主机号 ；
-p后跟端口号。
```

绑定了127.0.0.1则可以省略-h参数。不加-c则客户端不自动切换。

例如：客户端登录7000端口的，设置的数据应该存放在7001上则会报错请转到7001。而加上-c启动则会自动切换到7001客户端保存。

./src/redis-cli -c -h 10.211.55.15 -p 7000

向集群中添加新节点

新创建一个端口为7006的节点，连接上之后，查看内容结果如下，原因是没有加入集群

```
[root@server01 redis]# ./src/redis-cli -c -h 10.211.55.15 -p 7006
 10.211.55.15:7006> get jia
 (error) CLUSTERDOWN Hash slot not served
```

添加节点

```
./src/redis-trib.rb add-node 10.211.55.15:7006 10.211.55.15:7000
前面的IP加端口号是要添加的redis节点，后面的IP和端口号是集群中的任意一个节点。
[root@server01 redis]# ./src/redis-trib.rb add-node 10.211.55.15:7006 10.211.55.15:7000
 >>> Adding node 10.211.55.15:7006 to cluster 10.211.55.15:7000
 >>> Performing Cluster Check (using node 10.211.55.15:7000)
 M: 1e9bc711381b0193b5621d2be5456bf5e4224303 10.211.55.15:7000
  slots:0-5460 (5461 slots) master
  1 additional replica(s)
 S: bdca821feb591a2e3fc91f8b7010c8d4aff1d3f2 10.211.55.15:7005
  slots: (0 slots) slave
  replicates 198e315fae0ef1c85bea6bcf5ae9185f45d104df
 S: 793972e8b78d448a05f1ed3edd5cab637cd44ad8 10.211.55.15:7003
  slots: (0 slots) slave
  replicates 1e9bc711381b0193b5621d2be5456bf5e4224303
 M: f9f48f5e87b45945ed246717b8a29a07c06db20a 10.211.55.15:7001
  slots:5461-10922 (5462 slots) master
  1 additional replica(s)
 M: 198e315fae0ef1c85bea6bcf5ae9185f45d104df 10.211.55.15:7002
  slots:10923-16383 (5461 slots) master
  1 additional replica(s)
 S: 16ef7fcab93598cd8af05fe7100ce08e968fb2e8 10.211.55.15:7004
  slots: (0 slots) slave
  replicates f9f48f5e87b45945ed246717b8a29a07c06db20a
 [OK] All nodes agree about slots configuration.
 >>> Check for open slots...
 >>> Check slots coverage...
 [OK] All 16384 slots covered.
 >>> Send CLUSTER MEET to node 10.211.55.15:7006 to make it join the cluster.
 [OK] New node added correctly.
再次执行查看命令就可以得到正确的结果，因为已经成功的加入了新节点
[root@server01 redis]# ./src/redis-cli -c -h 10.211.55.15 -p 7006
 10.211.55.15:7006> get jia
 -> Redirected to slot [4511] located at 10.211.55.15:7000
 "hebi"
分配solt之前的情况
./src/redis-cli  -h 10.211.55.15 -p 7006
10.211.55.15:7006> g
 bdca821feb591a2e3fc91f8b7010c8d4aff1d3f2 10.211.55.15:7005 slave 198e315fae0ef1c85bea6bcf5ae9185f45d104df 0 1472918631906 3 connected
 16ef7fcab93598cd8af05fe7100ce08e968fb2e8 10.211.55.15:7004 slave f9f48f5e87b45945ed246717b8a29a07c06db20a 0 1472918635920 2 connected
 793972e8b78d448a05f1ed3edd5cab637cd44ad8 10.211.55.15:7003 slave 1e9bc711381b0193b5621d2be5456bf5e4224303 0 1472918633912 1 connected
 f9f48f5e87b45945ed246717b8a29a07c06db20a 10.211.55.15:7001 master - 0 1472918632908 2 connected 5461-10922
 1e9bc711381b0193b5621d2be5456bf5e4224303 10.211.55.15:7000 master - 0 1472918634916 1 connected 0-5460
 198e315fae0ef1c85bea6bcf5ae9185f45d104df 10.211.55.15:7002 master - 0 1472918636923 3 connected 10923-16383
 631894913b76ca87281e8d73accbce737581f3c4 10.211.55.15:7006 myself,master - 0 0 0 connected
给新节点分配solt
./src/redis-trib.rb reshard 10.211.55.15:7006
```


查看分配solt后的solt分配情况
```
[root@server01 redis]# ./src/redis-cli -h 10.211.55.15 -p 7006
 10.211.55.15:7006> cluster nodes
 bdca821feb591a2e3fc91f8b7010c8d4aff1d3f2 10.211.55.15:7005 slave 198e315fae0ef1c85bea6bcf5ae9185f45d104df 0 1472918869159 3 connected
 16ef7fcab93598cd8af05fe7100ce08e968fb2e8 10.211.55.15:7004 slave f9f48f5e87b45945ed246717b8a29a07c06db20a 0 1472918870164 2 connected
 793972e8b78d448a05f1ed3edd5cab637cd44ad8 10.211.55.15:7003 slave 1e9bc711381b0193b5621d2be5456bf5e4224303 0 1472918865140 1 connected
 f9f48f5e87b45945ed246717b8a29a07c06db20a 10.211.55.15:7001 master - 0 1472918866145 2 connected 10461-10922
 1e9bc711381b0193b5621d2be5456bf5e4224303 10.211.55.15:7000 master - 0 1472918867150 1 connected 0-5460
 198e315fae0ef1c85bea6bcf5ae9185f45d104df 10.211.55.15:7002 master - 0 1472918868153 3 connected 10923-16383
 631894913b76ca87281e8d73accbce737581f3c4 10.211.55.15:7006 myself,master - 0 0 7 connected 5461-10460
```

### 3、添加从节点

添加从节点：redis-trib.rb add-node 10.211.55.15:7007 10.211.55.15:7005
和上面情况类似，添加之后用redis-cli 登陆新添加的节点，然后执行设置主节点

设置主节点：cluster replicate

```
578d27842e8da87f89f14c73faf8f5bbe2f9ed85（对应master的nodeID）
```

删除集群节点

先删除主节点的情况

删除集群主节点

删除集群主节点之前要先将其上面的slot分配到其他主节点上

```
重新分配slot：redis-trib.rb reshard 192.168.72.100:7006
删除主节点：redis-trib.rb del-node 192.168.72.100:7006 578d27842e8da87f89f14c73faf8f5bbe2f9ed85
```

可以看到删除之后原来主节点的从节点自动变为其他主节点的从节点了（可以试验一下，观察一下该从节点与移动的slot有什么关系）

删除从节点

```
删除从节点：redis-trib.rb del-node 192.168.72.100:7007

4e3c459e26040f49b51dce8fdae5cb571b066ff0
```

先移除从节点的情况

先删除从节点

```
删除从节点：redis-trib.rbdel-node 192.168.72.100:7007

4e3c459e26040f49b51dce8fdae5cb571b066ff0
```

再删除主节点

```
重新分配slot：redis-trib.rb reshard 192.168.72.100:7006
```


然后再删除主节点

```
删除主节点：redis-trib.rb del-node 192.168.72.100:7006

578d27842e8da87f89f14c73faf8f5bbe2f9ed85
```


一般建议的如果要移除主节点，先将从节点移除，避免出错。
