project(adv_cmds)

cmake_minimum_required(VERSION 2.4.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_definitions(-DTARGET_OS_MAC=1)
add_definitions(-D__APPLE__ -D__DYNAMIC__)
# add_definitions(-DSUPPORT_UTMPX)

# Hide warnings
add_definitions(
	-Wno-#warnings
	-Wno-string-plus-int
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -U_POSIX_C_SOURCE -D__DARWIN_UNIX03 -nostdinc -fblocks -fPIC -ggdb -O0")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -U_POSIX_C_SOURCE -D__DARWIN_UNIX03 -nostdinc -nostdinc++ -fblocks -fPIC -ggdb -O0")
set(CMAKE_EXE_LINKER_FLAGS "-nodefaultlibs -nostdlib -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")
#set(CMAKE_SHARED_LINKER_FLAGS "-nodefaultlibs -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")

include_directories(${CMAKE_SOURCE_DIR}/src/libc/include/FreeBSD)
include_directories(${CMAKE_SOURCE_DIR}/src/libc/gen)
include_directories(${CMAKE_SOURCE_DIR}/src/libc/darwin)
include_directories(${CMAKE_SOURCE_DIR}/src/ncurses/include)
include_directories(${CMAKE_SOURCE_DIR}/src/external/libcxx/include)
include_directories(${CMAKE_SOURCE_DIR}/src/external/libcxxabi/include)

include(InstallSymlink)
set(BINARY_PACKAGING_MODE ON)

add_darling_executable(locale locale/locale.cc ${CMAKE_SOURCE_DIR}/src/libstdcxx/src/dso_handle.c)
target_link_libraries(locale cxx)

add_darling_executable(tabs tabs/tabs.c)
target_link_libraries(tabs ncurses)

add_darling_executable(tty tty/tty.c)
add_darling_executable(whois whois/whois.c)
add_darling_executable(stty stty/cchar.c  stty/gfmt.c  stty/key.c  stty/modes.c  stty/print.c  stty/stty.c  stty/util.c)
#add_darling_executable(pkill pkill/pkill.c) # doesn't build yet
add_darling_executable(ps ps/fmt.c  ps/keyword.c  ps/nlist.c  ps/print.c  ps/ps.c  ps/tasks.c)

install(TARGETS locale stty tabs tty whois ps
	DESTINATION libexec/darling/usr/bin)


set(man1
	cap_mkdb/cap_mkdb.1
	colldef/colldef.1
	finger/finger.1
	gencat/gencat.1
	last/last.1
	locale/locale.1
	localedef/localedef.1
	lsvfs/lsvfs.1
	mklocale/mklocale.1
	pkill/pkill.1
	ps/ps.1
	stty/stty.1
	tabs/tabs.1
	tty/tty.1
	whois/whois.1
)

install(FILES ${man1} DESTINATION libexec/darling/usr/share/man/man1)
install(FILES finger/finger.conf.5 DESTINATION libexec/darling/usr/share/man/man5)
install(FILES fingerd/fingerd.8 DESTINATION libexec/darling/usr/share/man/man8)
