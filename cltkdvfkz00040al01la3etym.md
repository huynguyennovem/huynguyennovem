---
title: "Compiling Flutter Engine Locally - Getting Started (Part I)"
seoTitle: "Compiling Flutter engine"
seoDescription: "Practical tutorials for compiling Flutter engine"
datePublished: Sat Mar 09 2024 17:52:38 GMT+0000 (Coordinated Universal Time)
cuid: cltkdvfkz00040al01la3etym
slug: compiling-flutter-engine-locally-part-i
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724419166443/96f196c6-2777-46d8-b4ff-c6b28315ae1f.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724419210075/5bfb11d0-d5c9-4c0a-9a73-eaa2df9df38b.jpeg
tags: tutorial, flutter, engine

---

Flutter empowers developers to dive deeper into the framework by compiling its engine locally. This process not only enables customization but also facilitates contributions to the open-source community. In this blog post, I will walk you through a step-by-step guide to preparing your development environment and compiling the Flutter engine locally.

## Prepare environment

The first step is to set up your development environment. Follow these steps to ensure you have all the necessary dependencies installed:

Step 1. Install dependencies at [Getting dependencies](https://github.com/flutter/flutter/wiki/Setting-up-the-Engine-development-environment#getting-dependencies):

```bash
git
ssh client
python3
Chromium's depot_tools
curl
unzip
Visual Studio, Windows 10 SDK (for Windows)
XCode, Rosetta (if using Mac M1)
```

Step 2. Head over to [https://github.com/flutter/engine](https://github.com/flutter/engine) and fork the repository into your own GitHub account.

Step 3. Open your terminal and create directory named `engine` and then cd to this directory:

```bash
➜  mkdir engine && cd engine
```

Step 4. Create `.gclient` file and edit it with the following content. Replace &lt;your\_github\_username&gt; with your actual GitHub username:

```bash
solutions = [
  {
    "managed": False,
    "name": "src/flutter",
    "url": "git@github.com:<your_github_username>/engine.git",
    "custom_deps": {},
    "deps_file": "DEPS",
    "safesync_url": "",
  },
]
```

Step 5. Fetch source code of environment for Flutter engine ([https://github.com/flutter/buildroot](https://github.com/flutter/buildroot))

```bash
➜  engine gclient sync
```

Result:

```bash
➜  engine pwd
/Users/huynq/Documents/GitHub/engine

➜  engine tree -L 2
.
└── src
    ├── AUTHORS
    ├── BUILD.gn
    ├── CODEOWNERS
    ├── LICENSE
    ├── README.md
    ├── build
    ├── build_overrides
    ├── buildtools
    ├── flutter
    ├── fuchsia
    ├── gpu
    ├── out
    ├── third_party
    └── tools

11 directories, 5 files
```

Step 6. Fetch engine source from remote ([https://github.com/flutter/engine](https://github.com/flutter/engine))

```bash
➜  engine cd src/flutter/
➜  flutter git:(main) git pull upstream main
From github.com:flutter/engine
 * branch                  main       -> FETCH_HEAD
Already up to date.
```

## Compile engine

Once you have set up your environment, you can proceed with compiling the Flutter engine.

**Step 1.** Fetch source code of environment again to update all dependencies.

```bash
➜  engine gclient sync
Updating depot_tools...
Syncing projects: 100% (141/141), done.                                                                            
Hook 'python3 src/flutter/tools/pub_get_offline.py' took 10.45 secs
Running hooks: 100% (15/15), done.
```

**Step 2.** Prepare build files for DEVICE-side executables

You can choose to compile for different platforms. For example, to compile for Android, use the following command:

```bash
➜  engine cd src/
➜  src git:(6e71c38) ./flutter/tools/gn --android --android-cpu arm64 --unoptimized
Generating GN files in: out/android_debug_unopt_arm64
Generating Xcode projects took 441ms
Generating compile_commands took 62ms
Done. Made 808 targets from 292 files in 6681ms
```

Note:

* If you build for ios, replace `--android` with `--ios` . For eg: `./flutter/tools/gn --ios --unoptimized`
    
* *There are some runtime modes you can select:* `--runtime-mode=[debug|profile|release]`
    

**Step 3.** Prepare the build files for HOST-side executables.

```bash
➜  src git:(6e71c38) ./flutter/tools/gn --unoptimized
Using prebuilt Dart SDK binary. If you are editing Dart sources and wish to compile the Dart SDK, set `--no-prebuilt-dart-sdk`.
Generating GN files in: out/host_debug_unopt
Generating Xcode projects took 436ms
Generating compile_commands took 84ms
Done. Made 938 targets from 335 files in 4135ms
```

*Note: There are some runtime modes you can select:* `--runtime-mode=[debug|profile|release]`

**Step 4**. Build your executables

These steps will take a long time, you should drink 2–3 cups of ☕️ while waiting 😌.

First, build for DEVICE-side executables. I build for my arm64 Android device (Realme 6, Android 11, arm64-v8a), so it's `android_debug_unopt_arm64`. If it's iOS: `ninja -C out/ios_debug_unopt`

```bash
➜  src git:(6e71c38) ninja -C out/android_debug_unopt_arm64
ninja: Entering directory `out/android_debug_unopt_arm64'
[209/5101] ACTION //flutter/shell/platform/android:flutter_shell_java(//build/toolchain/android:clang_arm64)
Note: Some input files use or override a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
Note: Some input files use unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
[5087/5098] ACTION //flutter/testing/scenario_...id_lint(//build/toolchain/android:clang_arm64)
Warning: This version only understands SDK XML versions up to 2 but an SDK XML file of version 3 was encountered. This can happen if you use versions of Android Studio and the command-line tools that were released at different times.
Warning: unexpected element (uri:"", local:"extension-level"). Expected elements are <{}codename>,<{}layoutlib>,<{}api-level>
Warning: unexpected element (uri:"", local:"base-extension"). Expected elements are <{}codename>,<{}layoutlib>,<{}api-level>
Note: Some input files use or override a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
[5089/5098] ACTION //flutter/testing/scenari...d_apk(//build/toolchain/android:clang_arm64)
Warning: This version only understands SDK XML versions up to 2 but an SDK XML file of version 3 was encountered. This can happen if you use versions of Android Studio and the command-line tools that were released at different times.
Warning: unexpected element (uri:"", local:"extension-level"). Expected elements are <{}codename>,<{}layoutlib>,<{}api-level>
Warning: unexpected element (uri:"", local:"base-extension"). Expected elements are <{}codename>,<{}layoutlib>,<{}api-level>
[5093/5098] ACTION //flutter/testing/scenari...t_apk(//build/toolchain/android:clang_arm64)
Warning: This version only understands SDK XML versions up to 2 but an SDK XML file of version 3 was encountered. This can happen if you use versions of Android Studio and the command-line tools that were released at different times.
Warning: unexpected element (uri:"", local:"extension-level"). Expected elements are <{}codename>,<{}layoutlib>,<{}api-level>
Warning: unexpected element (uri:"", local:"base-extension"). Expected elements are <{}codename>,<{}layoutlib>,<{}api-level>
Note: Some input files use or override a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
[5097/5097] STAMP obj/default.stamp
```

Next, build for HOST-side executables:

```bash
➜  src git:(6e71c38) ninja -C out/host_debug_unopt   
ninja: Entering directory `out/host_debug_unopt'
[0/1] Regenerating ninja files
[6194/6194] STAMP obj/default.stamp
```

**Step 5.** Use your build in Flutter project

Now that you have successfully compiled the Flutter engine, you can use it in your Flutter projects. To do this, run your Flutter project with the following parameters:

* `--local-engine`: the device executable you built above
    
* `--local-engine-src-path`: `engine/src` path
    
* `--local-engine-host`: `host_debug_unopt` output (\*)
    

> *(\*) Since Nov 2023, Flutter tool should require*`--local-engine-host`*when*`--local-engine`*is set, see issue*[https://github.com/flutter/flutter/issues/132245](https://github.com/flutter/flutter/issues/132245)

```bash
➜  new_3106 flutterm run --local-engine=android_debug_unopt_arm64 --local-engine-host=host_debug_unopt --local-engine-src-path=/Users/huynq/Documents/GitHub/engine/src
Launching lib/main.dart on RMX2001 in debug mode...
Running Gradle task 'assembleDebug'...                              5.3s
✓  Built build/app/outputs/flutter-apk/app-debug.apk.
D/ViewRootImpl(28547): enqueueInputEventMotionEvent { action=ACTION_DOWN, actionButton=0, id[0]=0, x[0]=964.0, y[0]=2138.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=277159252, downTime=277159252, deviceId=3, source=0x1002, displayId=0 }
D/ViewRootImpl[MainActivity](28547): processMotionEvent MotionEvent { action=ACTION_DOWN, actionButton=0, id[0]=0, x[0]=964.0, y[0]=2138.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=277159252, downTime=277159252, deviceId=3, source=0x1002, displayId=0 }
D/ViewRootImpl[MainActivity](28547): dispatchPointerEvent handled=true, event=MotionEvent { action=ACTION_DOWN, actionButton=0, id[0]=0, x[0]=964.0, y[0]=2138.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, classification=NONE, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=277159252, downTime=277159252, deviceId=3, source=0x1002, displayId=0 }
Syncing files to device RMX2001...                                 108ms

Flutter run key commands.
r Hot reload. 🔥🔥🔥
R Hot restart.
h List all available interactive commands.
d Detach (terminate "flutter run" but leave application running).
c Clear the screen
q Quit (terminate the application on the device).
```

## Troubleshoot

1. You may encounter `Unexpected Kernel Format Version 106` exception:
    

```console
➜  new_3106 flutterm run --local-engine=android_debug_unopt_arm64 --local-engine-host=host_debug_unopt --local-engine-src-path=/Users/huynq/Documents/GitHub/engine/src
Resolving dependencies... (1.0s)
> collection 1.17.2 (was 1.17.1)
> matcher 0.12.16 (was 0.12.15)
> material_color_utilities 0.5.0 (was 0.2.0) (0.8.0 available)
> source_span 1.10.0 (was 1.9.1)
> stack_trace 1.11.1 (was 1.11.0)
> stream_channel 2.1.2 (was 2.1.1)
> test_api 0.6.1 (was 0.5.1)
+ web 0.1.4-beta
These packages are no longer being depended on:
- js 0.6.7
Changed 9 dependencies!
Launching lib/main.dart on RMX2001 in debug mode...
Unhandled exception:
Unexpected Kernel Format Version 106 (expected 104)
#0      BinaryBuilder.readComponent.<anonymous closure> (package:kernel/binary/ast_from_binary.dart:691:9)
#1      Timeline.timeSync (dart:developer/timeline.dart:171:22)
#2      BinaryBuilder.readComponent (package:kernel/binary/ast_from_binary.dart:678:21)
#3      _InitializationFromSdkSummary._prepareSummary (package:front_end/src/fasta/incremental_compiler.dart:2469:12)
#4      _InitializationFromUri.initialize (package:front_end/src/fasta/incremental_compiler.dart:2543:23)
<asynchronous suspension>
#5      IncrementalCompiler._ensurePlatformAndInitialize (package:front_end/src/fasta/incremental_compiler.dart:1423:25)
<asynchronous suspension>
#6      IncrementalCompiler.computeDelta.<anonymous closure> (package:front_end/src/fasta/incremental_compiler.dart:312:11)
<asynchronous suspension>
#7      IncrementalCompiler.compile (package:vm/incremental_compiler.dart:75:50)
<asynchronous suspension>
#8      FrontendCompiler.compile (package:frontend_server/frontend_server.dart:599:11)
<asynchronous suspension>
#9      listenAndCompile.<anonymous closure> (package:frontend_server/frontend_server.dart:1310:11)
<asynchronous suspension>
the Dart compiler exited unexpectedly.
the Dart compiler exited unexpectedly.
Running Gradle task 'assembleDebug'...                                 ⣻%
```

**Solution**: Hint from [https://github.com/flutter/flutter/issues/50885#issuecomment-882045381](https://github.com/flutter/flutter/issues/50885#issuecomment-882045381)

Make sure you didn't forget to build for HOST-side executables:

```bash
./flutter/tools/gn --unoptimized
ninja -C out/host_debug_unopt
```

2. Depreciation issue by Python 3.12
    

```bash
➜  engine gclient sync
Syncing projects: 100% (137/137), done.                                                                            
Running hooks:  14% ( 2/14) Generate sdk/version
________ running 'python3 src/third_party/dart/tools/generate_sdk_version_file.py' in '/Users/huynq/Documents/GitHub/engine'
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:294: SyntaxWarning: invalid escape sequence '\$'
  ' awk "{ print \$1 }"',
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:385: SyntaxWarning: invalid escape sequence '\d'
  major = match_against('^MAJOR (\d+)$', content)
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:386: SyntaxWarning: invalid escape sequence '\d'
  minor = match_against('^MINOR (\d+)$', content)
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:387: SyntaxWarning: invalid escape sequence '\d'
  patch = match_against('^PATCH (\d+)$', content)
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:388: SyntaxWarning: invalid escape sequence '\d'
  prerelease = match_against('^PRERELEASE (\d+)$', content)
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:389: SyntaxWarning: invalid escape sequence '\d'
  prerelease_patch = match_against('^PRERELEASE_PATCH (\d+)$', content)
Traceback (most recent call last):
  File "/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/generate_sdk_version_file.py", line 7, in <module>
    import utils
  File "/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py", line 14, in <module>
    import imp
ModuleNotFoundError: No module named 'imp'
Error: Command 'python3 src/third_party/dart/tools/generate_sdk_version_file.py' returned non-zero exit status 1 in /Users/huynq/Documents/GitHub/engine
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:294: SyntaxWarning: invalid escape sequence '\$'
  ' awk "{ print \$1 }"',
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:385: SyntaxWarning: invalid escape sequence '\d'
  major = match_against('^MAJOR (\d+)$', content)
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:386: SyntaxWarning: invalid escape sequence '\d'
  minor = match_against('^MINOR (\d+)$', content)
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:387: SyntaxWarning: invalid escape sequence '\d'
  patch = match_against('^PATCH (\d+)$', content)
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:388: SyntaxWarning: invalid escape sequence '\d'
  prerelease = match_against('^PRERELEASE (\d+)$', content)
/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py:389: SyntaxWarning: invalid escape sequence '\d'
  prerelease_patch = match_against('^PRERELEASE_PATCH (\d+)$', content)
Traceback (most recent call last):
  File "/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/generate_sdk_version_file.py", line 7, in <module>
    import utils
  File "/Users/huynq/Documents/GitHub/engine/src/third_party/dart/tools/utils.py", line 14, in <module>
    import imp
ModuleNotFoundError: No module named 'imp'
```

This is a depreciation issue by using Python 3.12 in your system. Just downgrade or delete your python3 (which is Python 12) in your system.

E.g steps (macOS):

* Determine python version:
    
    ```bash
    ➜  ~ python3 -V
    Python 3.12.2
    ➜  ~ which python3
    /usr/local/bin/python3
    ```
    
* Go to `/usr/local/bin/python3`:
    
    ```bash
    ➜  bin ls | grep python
    python
    python-build
    python3
    python3-config
    python3.10
    python3.10-config
    python3.11
    python3.11-config
    python3.9
    python3.9-config
    ```
    
    Then delete `python3` and `python3-config`
    
* Retry:
    
    ```console
    ➜  engine gclient sync
    Syncing projects: 100% (137/137), done.                                                                            
    Hook 'python3 src/flutter/tools/pub_get_offline.py' took 14.53 secs
    Running hooks: 100% (14/14), done.
    ```
    

### Conclusion

Compiling the Flutter engine on local gives you the flexibility to customize and enhance the Flutter engine to suit your specific needs. This also be helpful to cherry-pick specific PR/fixes that haven't landed on the latest Flutter release yet. By following the steps mentioned in this article, you can set up your environment, compile the engine, and start contributing to the Flutter community as well.

So, go ahead, explore the possibilities, and happy compiling Flutter engine!

Stay in touch with me for updates:

* Twitter: [https://twitter.com/HuyNguyenTw](https://twitter.com/HuyNguyenTw)
    
* GitHub: [https://github.com/huynguyennovem](https://github.com/huynguyennovem)
    
* LinkedIn: [https://linkedin.com/in/huynguyennovem](https://linkedin.com/in/huynguyennovem)