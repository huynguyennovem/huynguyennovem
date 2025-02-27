---
title: "Compiling Flutter Engine Locally - What changes after monorepo (Part IV)"
seoTitle: "Compiling Flutter Engine Locally"
datePublished: Sun Feb 23 2025 07:04:06 GMT+0000 (Coordinated Universal Time)
cuid: cm7ha9exu000309jldhpj6lkd
slug: compiling-flutter-engine-locally-part-iv
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740543867115/58712380-f0eb-44ed-99d1-80dd839deb76.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1740294070058/3fb75603-a2c6-44bb-8673-7a77b5a1627a.jpeg
tags: flutter

---

Since Flutter monorepo is now complete, there are some updates to the engine compilation process. This article will guide you through the updated steps and help troubleshoot common issues you may encounter.

1. ## Preparing
    

First, ensure you have all required [dependencies](https://github.com/flutter/flutter/blob/master/engine/src/flutter/docs/contributing/Setting-up-the-Engine-development-environment.md#getting-dependencies). Refer to [Part I](https://huynq.dev/compiling-flutter-engine-locally-part-i) for an overview (note that it was written before the monorepo merged).

### Setting up `.gclient`

1. Navigate to `<flutter SDK>/engine/scripts`.
    
2. Copy the standard.gclient file to the Flutter SDK root directory.
    
3. Rename it to .gclient and place it in your Flutter SDK root. Follow the [official guide](https://github.com/flutter/flutter/blob/master/engine/README.md) for more details.
    

Example of `.gclient` content:

```json
solutions = [
  {
    "custom_deps": {},
    "deps_file": "DEPS",
    "managed": False,
    "name": ".",
    "safesync_url": "",

    # If you are using SSH to connect to GitHub, change the URL to:
    # git@github.com:flutter/flutter.git
    "url": "git@github.com:flutter/flutter.git",

    # Uncomment the custom_vars section below if you plan to build the web engine.
    # "custom_vars": {
    #   "download_emsdk": True,
    # },
  },
]
```

Run the following command to fetch dependencies:

```console
gclient sync -D
```

2. ## Compiling engine
    
    ### Prepare build files for DEVICE-side executables
    
    ```console
     cd engine/src
    ./flutter/tools/gn --android --android-cpu arm64 --unoptimized
    ```
    
    If you build for ios, replace `--android` with `--ios`:
    
    ```console
    ./flutter/tools/gn --ios --unoptimized
    ```
    
    ### Prepare the build files for HOST-side executables.
    
    ```console
    ./flutter/tools/gn --unoptimized
    ```
    
    You should now see the new output folders:
    
    ```console
    ➜  out git:(master) ✗ pwd
    /Users/huynq/Documents/GitHub/flutter_master/engine/src/out
    ➜  out git:(master) ✗ tree -L 1
    .
    ├── android_debug_unopt_arm64
    └── host_debug_unopt
    ```
    
    ### Build executables
    
    Building can take a long time, so grab a coffee or take a break!
    

```console
➜  src git:(master) ✗ ninja -C out/host_debug_unopt
ninja: Entering directory `out/host_debug_unopt'
[8308/8308] ACTION //flutter/lib/snapshot:create_macos_gen_snapshots(//build/toolchain/mac:clang_x64)
```

```bash
  src git:(master) ✗ ninja -C out/android_debug_unopt_arm64
ninja: Entering directory `out/android_debug_unopt_arm64'
[45/5875] ACTION //flutter/shell/platform/android:flutter_shell_java(//build/toolchain/android:clang_arm64)
Note: Some input files use or override a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
Note: Some input files use unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
[5875/5875] ACTION //flutter/shell/platform/android:flutter_jar_zip(//build/toolchain/android:clang_arm64)
```

If it's iOS:

```bash
ninja -C out/ios_debug_unopt
```

(make sure you `Prepare the build files` from the above step)

### Use your build in Flutter project

Check out [first article](https://huynq.dev/compiling-flutter-engine-locally-part-i) for details of parameters on integrating the compiled engine with your Flutter project.

3. ## Troubleshooting
    

3.1 Using deprecated `ANDROID_SDK_ROOT`

```console
➜  src git:(cc9bcdd) ninja -C out/android_debug_unopt_arm64
ninja: Entering directory `out/android_debug_unopt_arm64'
[78/6415] ACTION //flutter/shell/platform/android:flutter_shell_java(//build/toolchain/android:clang_arm64)
Note: Some input files use or override a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
Note: Some input files use unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
[6404/6415] ACTION //flutter/testing/scenario_app/android:android_lint(//build/toolchain/android:clang_arm64)
FAILED: scenario_app/reports/lint-results.xml 
vpython3 ../../flutter/testing/rules/run_gradle.py /Users/huynq/Documents/GitHub/flutter_beta/engine/src/flutter/testing/scenario_app/android lint --no-daemon -Pflutter_jar=/Users/huynq/Documents/GitHub/flutter_beta/engine/src/out/android_debug_unopt_arm64/flutter.jar -Pout_dir=/Users/huynq/Documents/GitHub/flutter_beta/engine/src/out/android_debug_unopt_arm64/scenario_app --project-cache-dir=/Users/huynq/Documents/GitHub/flutter_beta/engine/src/out/android_debug_unopt_arm64/scenario_app/.gradle --gradle-user-home=/Users/huynq/Documents/GitHub/flutter_beta/engine/src/out/android_debug_unopt_arm64/scenario_app/.gradle

FAILURE: Build failed with an exception.

* What went wrong:
Could not determine the dependencies of task ':app:lintReportDebug'.
> Several environment variables and/or system properties contain different paths to the SDK.
  Please correct and use only one way to inject the SDK location.
  
  ANDROID_HOME: /Users/huynq/Documents/GitHub/flutter_beta/engine/src/flutter/third_party/android_tools/sdk
  ANDROID_SDK_ROOT: /Users/huynq/Library/Android/sdk
  
  It is recommended to use ANDROID_HOME as other methods are deprecated

* Try:
> Run with --stacktrace option to get the stack trace.
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.
> Get more help at https://help.gradle.org.
```

The solution is to remove `ANDROID_SDK_ROOT` from path env if you set it to Android SDK.That's it!

3.2 ninja not found

```console
depot_tools/ninja.py: Could not find Ninja in the third_party of the current project, nor in your PATH.
Please take one of the following actions to install Ninja:
- If your project has DEPS, add a CIPD Ninja dependency to DEPS.
- Otherwise, add Ninja to your PATH *after* depot_tools.
```

Thank [jonahwilliams](https://github.com/jonahwilliams) for the [workaround](https://github.com/flutter/flutter/issues/163487#issuecomment-2673643365). I'm not sure how it's fixed later on (perhaps we will not need to add ninja to path on our side), but adding ninja from SDK can work around this issue:

```bash
export PATH=$HOME/flutter/engine/src/flutter/third_party/depot_tools/ninja:$PATH
export PATH=$HOME/flutter/engine/src/flutter/third_party/ninja:$PATH
```

Update: This issue (\[[163487](https://github.com/flutter/flutter/issues/163487)\]([https://github.com/flutter/flutter/issues/163487](https://github.com/flutter/flutter/issues/163487))) has been fixed, we don’t need this workaround anymore, just keep your path as is.

Check out my related articles for compiling Flutter engine series:

[https://huynq.dev/compiling-flutter-engine-locally-part-i](https://huynq.dev/compiling-flutter-engine-locally-part-i)

[https://huynq.dev/compiling-flutter-engine-locally-part-ii](https://huynq.dev/compiling-flutter-engine-locally-part-ii)

[https://huynq.dev/compiling-flutter-engine-locally-web-engine-part-iii](https://huynq.dev/compiling-flutter-engine-locally-web-engine-part-iii)