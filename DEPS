# Note: The buildbots evaluate this file with CWD set to the parent
# directory and assume that the root of the checkout is in ./v8/, so
# all paths in here must match this assumption.

vars = {
  "git_url": "https://chromium.googlesource.com",
}

deps = {
  "v8/build/gyp":
    Var("git_url") + "/external/gyp.git" + "@" + "01528c7244837168a1c80f06ff60fa5a9793c824",
  "v8/third_party/icu":
    Var("git_url") + "/chromium/deps/icu.git" + "@" + "94e4b770ce2f6065d4261d29c32683a6099b9d93",
  "v8/buildtools":
    Var("git_url") + "/chromium/buildtools.git" + "@" + "0c88009d5c50b9f21b6f8849db08abfb9eb29f1e",
  "v8/tools/swarming_client":
    Var('git_url') + '/external/swarming.client.git' + '@' + "e4c0e242eeec361886ed2b89591a505ebe24db43",
  "v8/testing/gtest":
    Var("git_url") + "/external/googletest.git" + "@" + "9855a87157778d39b95eccfb201a9dc90f6d61c6",
  "v8/testing/gmock":
    Var("git_url") + "/external/googlemock.git" + "@" + "0421b6f358139f02e102c9c332ce19a33faf75be",
  "v8/tools/clang":
    Var("git_url") + "/chromium/src/tools/clang.git" + "@" + "071be3c47dbf2dfb347629bbfb0afe462cbc571b",
}

deps_os = {
  "android": {
    "v8/third_party/android_tools":
      Var("git_url") + "/android_tools.git" + "@" + "4238a28593b7e6178c95431f91ca8c24e45fa7eb",
  },
  "win": {
    "v8/third_party/cygwin":
      Var("git_url") + "/chromium/deps/cygwin.git" + "@" + "c89e446b273697fadf3a10ff1007a97c0b7de6df",
  }
}

include_rules = [
  # Everybody can use some things.
  "+include",
  "+unicode",
  "+third_party/fdlibm",
]

# checkdeps.py shouldn't check for includes in these directories:
skip_child_includes = [
  "build",
  "third_party",
]

hooks = [
  {
    # This clobbers when necessary (based on get_landmines.py). It must be the
    # first hook so that other things that get/generate into the output
    # directory will not subsequently be clobbered.
    'name': 'landmines',
    'pattern': '.',
    'action': [
        'python',
        'v8/build/landmines.py',
    ],
  },
  # Pull clang-format binaries using checked-in hashes.
  {
    "name": "clang_format_win",
    "pattern": ".",
    "action": [ "download_from_google_storage",
                "--no_resume",
                "--platform=win32",
                "--no_auth",
                "--bucket", "chromium-clang-format",
                "-s", "v8/buildtools/win/clang-format.exe.sha1",
    ],
  },
  {
    "name": "clang_format_mac",
    "pattern": ".",
    "action": [ "download_from_google_storage",
                "--no_resume",
                "--platform=darwin",
                "--no_auth",
                "--bucket", "chromium-clang-format",
                "-s", "v8/buildtools/mac/clang-format.sha1",
    ],
  },
  {
    "name": "clang_format_linux",
    "pattern": ".",
    "action": [ "download_from_google_storage",
                "--no_resume",
                "--platform=linux*",
                "--no_auth",
                "--bucket", "chromium-clang-format",
                "-s", "v8/buildtools/linux64/clang-format.sha1",
    ],
  },
  # Pull luci-go binaries (isolate, swarming) using checked-in hashes.
  {
    'name': 'luci-go_win',
    'pattern': '.',
    'action': [ 'download_from_google_storage',
                '--no_resume',
                '--platform=win32',
                '--no_auth',
                '--bucket', 'chromium-luci',
                '-d', 'v8/tools/luci-go/win64',
    ],
  },
  {
    'name': 'luci-go_mac',
    'pattern': '.',
    'action': [ 'download_from_google_storage',
                '--no_resume',
                '--platform=darwin',
                '--no_auth',
                '--bucket', 'chromium-luci',
                '-d', 'v8/tools/luci-go/mac64',
    ],
  },
  {
    'name': 'luci-go_linux',
    'pattern': '.',
    'action': [ 'download_from_google_storage',
                '--no_resume',
                '--platform=linux*',
                '--no_auth',
                '--bucket', 'chromium-luci',
                '-d', 'v8/tools/luci-go/linux64',
    ],
  },
  # Pull binutils for linux, enabled debug fission for faster linking /
  # debugging when used with clang on Ubuntu Precise.
  # https://code.google.com/p/chromium/issues/detail?id=352046
  {
    'name': 'binutils',
    'pattern': 'v8/third_party/binutils',
    'action': [
        'python',
        'v8/third_party/binutils/download.py',
    ],
  },
  {
    # Pull gold plugin if needed or requested via GYP_DEFINES.
    # Note: This must run before the clang update.
    'name': 'gold_plugin',
    'pattern': '.',
    'action': ['python', 'v8/build/download_gold_plugin.py'],
  },
  {
    # Pull clang if needed or requested via GYP_DEFINES.
    # Note: On Win, this should run after win_toolchain, as it may use it.
    'name': 'clang',
    'pattern': '.',
    'action': ['python', 'v8/tools/clang/scripts/update.py', '--if-needed'],
  },
  {
    # A change to a .gyp, .gypi, or to GYP itself should run the generator.
    "pattern": ".",
    "action": ["python", "v8/build/gyp_v8"],
  },
]
