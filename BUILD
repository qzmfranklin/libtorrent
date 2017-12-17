licenses(['notice'])
package(default_visibility=['//visibility:public'])

# How to write and update this Bazel BUILD file?
#
# Because the official github repository,
#       https://github.com/arvidn/libtorrent
# does not support building with Bazel, it is generally painful to update such a
# Bazel BUILD file.
#
# Unfortunately, I do not have a one-off answer.  The best I can do is to
# describe the path I took to get here.
#
#   1.  Understand the existing build system.
#       For libtorrent, this means reading the page below thoroughly:
#           https://github.com/arvidn/libtorrent/blob/master/docs/building.rst
#
#   2.  Try with the native build system.
#       This is simple.  Everything just works, assuming you follow the
#       instructions correctly.
#
#   3.  Find the exact commands that the native build system used.
#       This usually means:
#           make -j8 &> build.log
#
#   4.  Transcribe the commands into Bazel.
#
# I Bazelised the build of many open source C/C++ projects using the above
# described approach.  Hope it works for you too.


libtorrent_copts= [
    # Suppress known warnings.
    '-Wno-deprecated-declarations',

    # Select the 1.0 version of the openssl API.
    '-DOPENSSL_API_COMPAT=0x10000000L',

    # The actual flags extracted from a legit build.
    '-DBOOST_ASIO_ENABLE_CANCELIO=1',
    '-DBOOST_ASIO_HAS_STD_CHRONO=1',
    '-DBOOST_EXCEPTION_DISABLE=1',
    '-DHAVE_BOOST=/\\*\\*/',
    '-DHAVE_BOOST_SYSTEM=/\\*\\*/',
    '-DHAVE_CLOCK_GETTIME=1',
    '-DHAVE_CXX11=1',
    '-DHAVE_DLFCN_H=1',
    '-DHAVE_INTTYPES_H=1',
    '-DHAVE_MEMORY_H=1',
    '-DHAVE_PTHREAD=1',
    '-DHAVE_STDINT_H=1',
    '-DHAVE_STDLIB_H=1',
    '-DHAVE_STRINGS_H=1',
    '-DHAVE_STRING_H=1',
    '-DHAVE_SYS_STAT_H=1',
    '-DHAVE_SYS_TYPES_H=1',
    '-DHAVE_UNISTD_H=1',
    '-DLT_OBJDIR=\\".libs/\\"',
    '-DNDEBUG=1',
    '-DPACKAGE=\\"libtorrent-rasterbar\\"',
    '-DPACKAGE_BUGREPORT=\\"arvid@libtorrent.org\\"',
    '-DPACKAGE_NAME=\\"libtorrent-rasterbar\\"',
    '-DPACKAGE_STRING=\\"libtorrent-rasterbar\\ 1.2.0\\"',
    '-DPACKAGE_TARNAME=\\"libtorrent-rasterbar\\"',
    '-DPACKAGE_URL=\\"http://www.libtorrent.org\\"',
    '-DPACKAGE_VERSION=\\"1.2.0\\"',
    '-DSTDC_HEADERS=1',
    '-DTORRENT_BUILDING_SHARED=1',
    '-DTORRENT_USE_INVARIANT_CHECKS=0',
    '-DTORRENT_USE_LIBCRYPTO=1',
    '-DTORRENT_USE_OPENSSL=1',
    '-DVERSION=\\"1.2.0\\"',
    '-Wno-format-zero-length',
    '-ftemplate-depth=512',
    '-fvisibility-inlines-hidden',
    # TODO (zhongming): Enable -fvisibility=hidden after marking at least the
    # following functions public:
    #       libtorrent::natpmp::delete_mapping
    #       libtorrent::natpmp::add_mapping
    #       libtorrent::print_address
    #       libtorrent::natpmp::close
    # Here is a blog explaining why we should use -fvisibility=hidden:
    #       https://gcc.gnu.org/wiki/Visibility
    #'-fvisibility=hidden',
    '-std=c++11',
]

cc_library(
    name = 'libtorrent',
    srcs = glob([
        'ed25519/src/*.cpp',
        'src/*.cpp',
        'src/kademlia/*.cpp',
    ]),
    hdrs = glob([
        'ed25519/src/*.h',
        'include/libtorrent/**/*.hpp',
        'include/libtorrent/ConvertUTF.h'
    ]),
    includes = [
        'include',
        'ed25519/src',
    ],
    copts = libtorrent_copts,
    linkopts = [
        '-ldl',
        '-lm',
        '-pthread',
    ],
    deps = [
        '//third_party/cc/boost:asio',
        '//third_party/cc/boost:crc',
        '//third_party/cc/boost:date_time',
        '//third_party/cc/boost:lexical_cast',
        '//third_party/cc/boost:multiprecision',
        '//third_party/cc/boost:pool',
        '//third_party/cc/boost:system',
        '//third_party/cc/boost:type_traits',
        '//third_party/cc/boost:variant',
        '//third_party/cc/openssl:ssl',
    ],
)

cc_binary(
    name = 'client',
    srcs = [
        'examples/client_test.cpp',
        'examples/print.cpp',
        'examples/print.hpp',
        'examples/session_view.cpp',
        'examples/session_view.hpp',
        'examples/torrent_view.cpp',
        'examples/torrent_view.hpp',
    ],
    deps = [
        ':libtorrent',
    ],
    copts = libtorrent_copts,
)


[cc_binary(
    name = name,
    srcs = [
        'examples/%s.cpp' % name,
    ],
    deps = [
        ':libtorrent',
    ],
    copts = libtorrent_copts,
) for name in [
        'connection_tester',
        'dump_torrent',
        'make_torrent',
        'simple_client',
        'stats_counters',
        'upnp_test',
    ]
]


[cc_test(
    name = f,
    srcs = [
        'test/%s.cpp' % f,
    ],
    copts = libtorrent_copts,
    deps = [
        ':libtorrent',
    ],
) for f in [
        'test_natpmp',
        'enum_if',
    ]
]

# This cc_library has the main() function.  The tests only need to link against
# this library to become executables.
cc_library(
    name = 'libtorrent_test_main',
    visibility = [
        '//visibility:__pkg__',
    ],
    testonly = 1,
    srcs = [
	    'test/bittorrent_peer.cpp',
	    'test/dht_server.cpp',
	    'test/main.cpp',
	    'test/make_torrent.cpp',
	    'test/peer_server.cpp',
	    'test/print_alerts.cpp',
	    'test/settings.cpp',
	    'test/setup_transfer.cpp',
	    'test/swarm_suite.cpp',
	    'test/test.cpp',
	    'test/test_utils.cpp',
	    'test/udp_tracker.cpp',
	    'test/web_seed_suite.cpp',
    ] + glob([
        'test/*.hpp'
    ]),
    copts = libtorrent_copts + [
        '-DTORRENT_BUILDING_TEST_SHARED',
        '-DED25519_BUILD_DLL',
    ],
    deps = [
        ':libtorrent',
    ],
)

[cc_test(
    name = f,
    testonly = 1,
    srcs = [
        'test/%s.cpp' % f,
    ],
    copts = libtorrent_copts,
    deps = [
        ':libtorrent_test_main',
    ],
) for f in [
        # TODO (zhongming): 22 tests fail.  Fix them!
        'test_alert_manager',
        'test_alert_types',
        'test_alloca',
        'test_auto_unchoke',
        'test_bandwidth_limiter',
        'test_bdecode',
        'test_bencoding',
        'test_bitfield',
        'test_block_cache',
        'test_bloom_filter',
        'test_buffer',
        'test_checking',
        'test_crc32',
        'test_create_torrent',
        'test_dht',
        'test_dht_storage',
        'test_direct_dht',
        'test_dos_blocker',
        'test_ed25519',
        'test_enum_net',
        'test_fast_extension',
        'test_fence',
        'test_ffs',
        'test_file',
        'test_file_progress',
        'test_file_storage',
        'test_flags',
        'test_gzip',
        'test_hasher',
        'test_hasher512',
        'test_heterogeneous_queue',
        'test_http_connection',
        'test_http_parser',
        'test_identify_client',
        'test_ip_filter',
        'test_ip_voter',
        'test_linked_list',
        'test_listen_socket',
        'test_magnet',
        'test_merkle',
        'test_packet_buffer',
        'test_part_file',
        'test_pe_crypto',
        'test_peer_classes',
        'test_peer_list',
        'test_peer_priority',
        'test_pex',
        'test_piece_picker',
        'test_primitives',
        'test_priority',
        'test_privacy',
        'test_random',
        'test_read_piece',
        'test_read_resume',
        'test_receive_buffer',
        'test_recheck',
        'test_remap_files',
        'test_remove_torrent',
        'test_resolve_links',
        'test_resume',
        'test_session',
        'test_session_params',
        'test_settings_pack',
        'test_sha1_hash',
        'test_sliding_average',
        'test_socket_io',
        'test_span',
        'test_ssl',
        'test_stack_allocator',
        'test_stat_cache',
        'test_storage',
        'test_string',
        'test_tailqueue',
        'test_threads',
        'test_time',
        'test_time_critical',
        'test_timestamp_history',
        'test_torrent',
        'test_torrent_info',
        'test_tracker',
        'test_transfer',
        'test_url_seed',
        'test_utf8',
        'test_utp',
        'test_web_seed',
        'test_web_seed_ban',
        'test_web_seed_chunked',
        'test_web_seed_http',
        'test_web_seed_http_pw',
        'test_web_seed_redirect',
        'test_web_seed_socks4',
        'test_web_seed_socks5',
        'test_web_seed_socks5_no_peers',
        'test_web_seed_socks5_pw',
        'test_xml',

        # Quote from the Jamfile:
        #       Turn these tests into simulations
        'test_upnp',
        'test_lsd',
    ]
]
