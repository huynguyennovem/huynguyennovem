---
title: "Compiling Flutter Engine Locally - Apply a fixed commit (Part II)"
seoTitle: "Compiling Flutter engine"
seoDescription: "Practical tutorials for compiling Flutter engine"
datePublished: Sat Mar 09 2024 17:56:22 GMT+0000 (Coordinated Universal Time)
cuid: cltke08ht00010akz98t3ddb4
slug: compiling-flutter-engine-locally-part-ii
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724419466435/1553e60c-715f-423d-99a0-6788c7accf06.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724419495250/f03d1ec7-f001-4026-953e-4a3ee4571ee0.jpeg
tags: tutorial, flutter, engine

---

Welcome back to the second part of the series on compiling the Flutter engine locally. In the first article, we learned how to build the engine using the latest version. Now, we'll explore how to build the engine on a specific version and apply a fixed commit to it for your desired engine version.

The example I will use in this article: [https://github.com/flutter/flutter/issues/130476](https://github.com/flutter/flutter/issues/130476)

## Issue analysis

* The bug doesn't occur on stable `3.10.6` but on master `3.13.0-3.0.pre.21` (as OP reported). The current master is `3.13.0-7.0.pre.57` (the released version I have on my machine now) and the issue is already fixed on this version.
    
* The bug is fixed by [https://github.com/flutter/engine/pull/43695](https://github.com/flutter/engine/pull/43695). The commit is [https://github.com/flutter/engine/commit/bf6d4bfe27cca6e6189382d17e5d371162492a27](https://github.com/flutter/engine/commit/bf6d4bfe27cca6e6189382d17e5d371162492a27):
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710006843724/6ef7a8bc-927e-4f1a-97ab-8640af201da9.png align="center")
    
* The fix is only available on these master versions: `3.13.0-7.0.pre`, `3.13.0-6.0.pre`, `3.13.0-5.0.pre`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710006875089/33c18764-98c8-4416-b48c-d1e20a5b6fe1.png align="center")

* The bug appears on master version `3.13.0-3.0.pre.21` which has engine revision `1b1ccdd1f5` (you can check this from `flutter doctor -v` of OP report)
    

👉 The final goal is building an engine version which is started from revision of Flutter master `3.13.0-3.0.pre.21`, and then cherry-pick the fix commit above. To make it easier to imagine:

> engine output X = 3.13.0-3.0.pre.21 + bf6d4bfe27cca6e6189382d17e5d371162492a27).

Let's go through the steps below section by section. The first section is intended to build the engine at a specific released version. But if you only need to build engine with fix (the final goal), you can skip the 1st section and go to 2nd section.

## Build engine

### 1\. Build engine on a specific released version

Currently, my local engine repository (`src/flutter`) is on the latest version `3.13.0-7.0.pre.57`. But I want to build engine at version `3.13.0-3.0.pre.21`. The bug can be reproducible on this version, so I can verify the result by checking the bug after building process finished.

* Step 1: cd to main engine directory (`src/flutter` is actually engine repository, not `engine` directory outside)
    

```bash
➜  engine cd src/flutter/
```

* Step 2: Checkout new branch from that revision, named it `master-313-030-pre21-build-engine`:
    

```bash
 ➜  flutter git:(main) git checkout -b master-313-030-pre21-build-engine 1b1ccdd1f5 
Updating files: 100% (1799/1799), done.
Switched to a new branch 'master-313-030-pre21-build-engine'
```

* Step 3: Start building it as previous guide (without needing to rebuild files for device-side and host-side executables)
    

```bash
➜  engine gclient sync
Updating depot_tools...
Syncing projects: 100% (141/141), done.                                                                            
Hook 'python3 src/flutter/tools/pub_get_offline.py' took 16.11 secs
Running hooks: 100% (15/15), done.                           

➜  engine cd src 

➜  src git:(master) ninja -C out/host_debug_unopt
ninja: Entering directory `out/host_debug_unopt'
[0/1] Regenerating ninja files
[3077/3077] STAMP obj/default.stamp

➜  src git:(master) ninja -C out/ios_debug_unopt
ninja: Entering directory `out/ios_debug_unopt'
[0/1] Regenerating ninja files
[1639/1639] STAMP obj/default.stamp

➜  new_3106 flutterm run --local-engine=ios_debug_unopt --local-engine-src-path=/Users/huynq/Documents/GitHub/engine/src
Launching lib/main.dart on iPhone in debug mode...
Automatically signing iOS for device deployment using specified development team in Xcode project: 
Running Xcode build...                                                  
 └─Compiling, linking and signing...                         6.5s
Xcode build done.                                           17.5s

[VERBOSE-2:validation.cc(49)] Break on 'ImpellerValidationBreak' to inspect point of failure: Could not find glyph position in the atlas.
Installing and launching...                                        17.2s
Syncing files to device iPhone...                                   81ms

Flutter run key commands.
r Hot reload. 🔥🔥🔥
R Hot restart.
h List all available interactive commands.
d Detach (terminate "flutter run" but leave application running).
c Clear the screen
q Quit (terminate the application on the device).

A Dart VM Service on iPhone is available at: http://127.0.0.1:57579/aq7ACWb5yII=/
The Flutter DevTools debugger and profiler on iPhone is available at: http://127.0.0.1:9101?uri=http://127.0.0.1:57579/aq7ACWb5yII=/
```

Nice, I can see the bug now with the wrong UI on iPhone and there are many logs `ImpellerValidationBreak` in the output console, it means the engine is built at released version `3.13.0-3.0.pre.21` successfully. You can then use this build to test or validate any issues you encountered as well.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710006912566/daa7c00f-07f1-4195-b9b4-08ee47a4732f.png align="center")

### 2\. Build engine with a fix

Now, let's say you encounter a specific bug that has already been fixed in the Flutter engine's latest version, but you don't want to upgrade the SDK entirely. You can cherry-pick the fix into your local engine build to address the issue. Here's how:

* Step 1: Let's make sure the previous branch is clean with no local change. Otherwise, let's delete the current local branch created above `master-313-030-pre21-build-engine` and recreate it. (Skip this step if you haven't tried the 1st section yet)
    

```bash
➜  flutter git:(main) git branch -d master-313-030-pre21-build-engine
Deleted branch master-313-030-pre21-build-engine (was 1b1ccdd1f5).

➜  flutter git:(1b1ccdd1f5) git checkout -b master-313-030-pre21-build-engine 1b1ccdd1f5
Switched to a new branch 'master-313-030-pre21-build-engine'
```

* Step 2: pick the commit that fixed the bug. There might be some conflicts, you should solve them and commit changes to your created branch (`master-313-030-pre21-build-engine`). The good commit can be found in section `First analysis` above.
    

```bash
➜  flutter git:(master-313-030-pre21-build-engine) ✗ git cherry-pick bf6d4bfe27cca6e6189382d17e5d371162492a27
error: could not apply bf6d4bfe27... [Impeller] Fix text scaling issues again, this time with perspective when a save layer is involved (#43695)
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'

➜  flutter git:(master-313-030-pre21-build-engine) ✗ git add .

➜  flutter git:(master-313-030-pre21-build-engine) ✗ git commit -m "pick fix for issue 130476"                           
[master-313-030-pre21-build-engine d6a51cc0c1] pick fix for issue 130476
6 files changed, 46 insertions(+), 8 deletions(-)

➜  flutter git:(master-313-030-pre21-build-engine) git log --oneline
d6a51cc0c1 (HEAD -> master-313-030-pre21-build-engine) pick fix for issue 130476
1b1ccdd1f5 Roll Skia from 4e989b1564ee to bedc92598644 (1 revision) (#43617)
cf2fa190fd move rtree and canvas_spy sources to Fuchsia sub-directory (#43615)
...
```

* Step 3: Now you have the engine source as expected, start building engine as the previous guide (same as the previous section, no need to rebuild files for device-side and host-side executables)
    

```bash
➜  flutter git:(master-313-030-pre21-build-engine) cd ../..

➜  engine gclient sync
Updating depot_tools...
Syncing projects: 100% (141/141), done.                                                                            
Hook 'python3 src/flutter/tools/pub_get_offline.py' took 11.47 secs
Running hooks: 100% (15/15), done.  

➜  engine cd src

➜  src git:(master) ninja -C out/host_debug_unopt
ninja: Entering directory `out/host_debug_unopt'
[0/1] Regenerating ninja files
[135/1722] CXX obj/flutter/impeller/compiler/impellerc_unittests.compiler_t[157/1722] CX[197/1722] CXX obj/flutter/impeller/tessellator/tessellator.tessellator.o
[1717/1717] STAMP obj/default.stamp

➜  src git:(master) ninja -C out/ios_debug_unopt
ninja: Entering directory `out/ios_debug_unopt'
[0/1] Regenerating ninja files
[965/965] STAMP obj/default.stamp
```

* Step 4: Run the new engine build on sample code to verify if the bug has been fixed:
    

```bash
➜  new_3106 flutterm run --local-engine=ios_debug_unopt --local-engine-host=host_debug_unopt --local-engine-src-path=/Users/huynq/Documents/GitHub/engine/src
Launching lib/main.dart on iPhone in debug mode...
Automatically signing iOS for device deployment using specified development team in Xcode project: 
Running Xcode build...                                                  
 └─Compiling, linking and signing...                         8.8s
Xcode build done.                                           23.4s
(lldb) 2023-07-24 01:43:18.641430+0700 Runner[24931:2805923] [VERBOSE-1:FlutterView.mm(72)] Rendering wide gamut colors is turned on but isn't supported, downgrading the color gamut to sRGB.
[VERBOSE-2:FlutterDarwinContextMetalImpeller.mm(37)] Using the Impeller rendering backend.
flutter: A message on the flutter/lifecycle channel was discarded before it could be handled.
This happens when a plugin sends messages to the framework side before the framework has had an opportunity to register a listener. See the ChannelBuffers API documentation for details on how to configure the channel to expect more messages, or to expect messages to get discarded:
  https://api.flutter.dev/flutter/dart-ui/ChannelBuffers-class.html
The capacity of the flutter/lifecycle channel is 1 message.
flutter: A message on the flutter/lifecycle channel was discarded before it could be handled.
This happens when a plugin sends messages to the framework side before the framework has had an opportunity to register a listener. See the ChannelBuffers API documentation for details on how to configure the channel to expect more messages, or to expect messages to get discarded:
  https://api.flutter.dev/flutter/dart-ui/ChannelBuffers-class.html
The capacity of the flutter/lifecycle channel is 1 message.
Installing and launching...                                        26.4s
Syncing files to device iPhone...                                   79ms

Flutter run key commands.
r Hot reload. 🔥🔥🔥
R Hot restart.
h List all available interactive commands.
d Detach (terminate "flutter run" but leave application running).
c Clear the screen
q Quit (terminate the application on the device).

A Dart VM Service on iPhone is available at: http://127.0.0.1:51251/it0Y8nWAtVI=/
The Flutter DevTools debugger and profiler on iPhone is available at: http://127.0.0.1:9101?uri=http://127.0.0.1:51251/it0Y8nWAtVI=/
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710006944237/81c2317b-3e66-494d-9c60-9213d13d337b.webp align="center")

## Troubleshoot

*1\. Can not checkout specific engine revision*

Try fetching all branches:

```bash
➜  flutter git:(main) git checkout -b master-313-030-pre21-build-engine 1b1ccdd1f5
fatal: '1b1ccdd1f5' is not a commit and a branch 'master-313-030-pre21-build-engine' cannot be created from it

➜  flutter git:(main) git fetch --all
Fetching origin
Fetching upstream
remote: Enumerating objects: 412516, done.
remote: Counting objects: 100% (7692/7692), done.
remote: Compressing objects: 100% (121/121), done.
remote: Total 412516 (delta 7606), reused 7635 (delta 7568), pack-reused 404824
Receiving objects: 100% (412516/412516), 226.28 MiB | 7.28 MiB/s, done.
Resolving deltas: 100% (316772/316772), completed with 1100 local objects.
From github.com:flutter/engine
 * [new branch]            CaseyHillers-patch-1                              -> upstream/CaseyHillers-patch-1
 * [new branch]            CaseyHillers-patch-2                              -> upstream/CaseyHillers-patch-2
 ....
```

```bash
 ➜  flutter git:(main) git checkout -b master-313-030-pre21-build-engine 1b1ccdd1f5 
Updating files: 100% (1799/1799), done.
Switched to a new branch 'master-313-030-pre21-build-engine'
```

*2\. Failed when building host-side executable*

```bash
➜  src git:(master) ninja -C out/host_debug_unopt
ninja: Entering directory `out/host_debug_unopt'
[8/1668] CXX obj/flutter/display_list/effects/display_list_unittests.dl_image_filter_unittests.o
FAILED: obj/flutter/display_list/effects/display_list_unittests.dl_image_filter_unittests.o 
../../buildtools/mac-x64/clang/bin/clang++ -MD -MF obj/flutter/display_list/effects/display_list_unittests.dl_image_filter_unittests.o.d -DUSE_OPENSSL=1 -D__STDC_CONSTANT_MACROS -D__STDC_FORMAT_MACROS -D_FORTIFY_SOURCE=2 -D_LIBCPP_DISABLE_AVAILABILITY=1 -D_LIBCPP_DISABLE_VISIBILITY_ANNOTATIONS -D_LIBCPP_ENABLE_THREAD_SAFETY_ANNOTATIONS -D_DEBUG -DSK_TYPEFACE_FACTORY_CORETEXT -DSK_GL -DSK_METAL -DSK_ENABLE_DUMP_GPU -DSK_CODEC_DECODES_JPEG -DSK_CODEC_DECODES_PNG -DSK_CODEC_DECODES_WEBP -DSK_HAS_WUFFS_LIBRARY -DSK_XML -DFLUTTER_RUNTIME_MODE_DEBUG=1 -DFLUTTER_RUNTIME_MODE_PROFILE=2 -DFLUTTER_RUNTIME_MODE_RELEASE=3 -DFLUTTER_RUNTIME_MODE_JIT_RELEASE=4 -DDART_LEGACY_API=\[\[deprecated\]\] -DFLUTTER_RUNTIME_MODE=1 -DFLUTTER_JIT_RUNTIME=1 -DIMPELLER_DEBUG=1 -DIMPELLER_SUPPORTS_RENDERING=1 -DIMPELLER_ENABLE_METAL=1 -DIMPELLER_ERROR_CHECK_ALL_GL_CALLS -DSK_FORCE_AAA -DSK_LEGACY_IGNORE_DRAW_VERTICES_BLEND_WITH_NO_SHADER -DSK_DISABLE_LEGACY_SHADERCONTEXT -DSK_DISABLE_LOWP_RASTER_PIPELINE -DSK_FORCE_RASTER_PIPELINE_BLITTER -DSK_METAL_WAIT_UNTIL_SCHEDULED -DSK_DISABLE_EFFECT_DESERIALIZATION -DSK_ENABLE_SKSL -DSK_ENABLE_SKSL_IN_RASTER_PIPELINE -DSK_ENABLE_PRECOMPILE -DSK_GANESH -DSK_USE_PERFETTO -DSK_ENABLE_API_AVAILABLE -I../.. -Igen -I../../third_party/libcxx/include -I../../third_party/libcxxabi/include -I../../build/secondary/third_party/libcxx/config -I../../flutter -Igen/flutter -I../../third_party/flatbuffers/include -I../../third_party/skia -I../../third_party/googletest/googlemock/include -I../../third_party/googletest/googletest/include -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX13.3.sdk -mmacosx-version-min=10.14.0 -fno-strict-aliasing -fstack-protector-all -arch x86_64 -fcolor-diagnostics -Wall -Wextra -Wendif-labels -Werror -Wno-missing-field-initializers -Wno-unused-parameter -Wno-unused-but-set-parameter -Wno-unused-but-set-variable -Wno-implicit-int-float-conversion -Wno-c99-designator -Wno-deprecated-copy -Wno-psabi -Wno-unqualified-std-cast-call -Wno-non-c-typedef-for-linkage -Wno-range-loop-construct -Wunguarded-availability -Wno-deprecated-declarations -fvisibility=hidden -Wstring-conversion -Wnewline-eof -O0 -g2 -Wno-newline-eof -fvisibility-inlines-hidden -std=c++17 -fno-rtti -nostdinc++ -nostdinc++ -fvisibility=hidden -fno-exceptions -stdlib=libc++ -Wno-inconsistent-missing-override  -c ../../flutter/display_list/effects/dl_image_filter_unittests.cc -o obj/flutter/display_list/effects/display_list_unittests.dl_image_filter_unittests.o
../../flutter/display_list/effects/dl_image_filter_unittests.cc:687:11: error: use of undeclared identifier 'SkColorFilters'; did you mean 'SkColorFilter'?
          SkColorFilters::Blend(SK_ColorRED, SkBlendMode::kSrcOver), nullptr),
          ^~~~~~~~~~~~~~
          SkColorFilter
../../third_party/skia/include/effects/SkImageFilters.h:30:7: note: 'SkColorFilter' declared here
class SkColorFilter;
      ^
../../flutter/display_list/effects/dl_image_filter_unittests.cc:687:11: error: incomplete type 'SkColorFilter' named in nested name specifier
          SkColorFilters::Blend(SK_ColorRED, SkBlendMode::kSrcOver), nullptr),
          ^~~~~~~~~~~~~~~~
../../third_party/skia/include/core/SkImageFilter.h:15:7: note: forward declaration of 'SkColorFilter'
class SkColorFilter;
      ^
../../flutter/display_list/effects/dl_image_filter_unittests.cc:687:46: error: incomplete type 'SkBlendMode' named in nested name specifier
          SkColorFilters::Blend(SK_ColorRED, SkBlendMode::kSrcOver), nullptr),
                                             ^~~~~~~~~~~~~
../../flutter/display_list/effects/dl_image_filter_unittests.cc:694:15: error: use of undeclared identifier 'SkColorFilters'; did you mean 'SkColorFilter'?
              SkColorFilters::Blend(SK_ColorRED, SkBlendMode::kSrcOver),
              ^~~~~~~~~~~~~~
              SkColorFilter
../../third_party/skia/include/effects/SkImageFilters.h:30:7: note: 'SkColorFilter' declared here
class SkColorFilter;
      ^
../../flutter/display_list/effects/dl_image_filter_unittests.cc:694:15: error: incomplete type 'SkColorFilter' named in nested name specifier
              SkColorFilters::Blend(SK_ColorRED, SkBlendMode::kSrcOver),
              ^~~~~~~~~~~~~~~~
../../third_party/skia/include/core/SkImageFilter.h:15:7: note: forward declaration of 'SkColorFilter'
class SkColorFilter;
      ^
../../flutter/display_list/effects/dl_image_filter_unittests.cc:694:50: error: incomplete type 'SkBlendMode' named in nested name specifier
              SkColorFilters::Blend(SK_ColorRED, SkBlendMode::kSrcOver),
                                                 ^~~~~~~~~~~~~
6 errors generated.
[17/1668] CXX obj/flutter/display_list/testing/display_list_testing.dl_test_snippets.o
ninja: build stopped: subcommand failed.
```

This may be caused by you haven't run `gclient sync` at engine directory. Make sure you ran `gclient sync` before building executables.

```bash
➜  engine gclient sync
Updating depot_tools...
Syncing projects: 100% (141/141), done.                                                                            
Hook 'python3 src/flutter/tools/pub_get_offline.py' took 16.11 secs
Running hooks: 100% (15/15), done.                           
➜  engine cd src 
➜  src git:(master) ninja -C out/host_debug_unopt
ninja: Entering directory `out/host_debug_unopt'
[0/1] Regenerating ninja files
[3077/3077] STAMP obj/default.stamp
➜  src git:(master) ninja -C out/ios_debug_unopt
ninja: Entering directory `out/ios_debug_unopt'
[0/1] Regenerating ninja files
[1639/1639] STAMP obj/default.stamp
```

## Conclusion

Congratulations! You've now mastered the art of compiling the Flutter engine locally on specific versions and applying fixes. Armed with this knowledge, you can confidently tackle various development scenarios and verify bug fixes without the need to upgrade to the latest SDK version.

Now, armed with your newfound expertise, go forth and build amazing Flutter applications with confidence, knowing you have the power to control and customize the engine to suit your needs.

Happy Fluttering!

Stay in touch with me for updates:

* Twitter: [https://twitter.com/HuyNguyenTw](https://twitter.com/HuyNguyenTw)
    
* GitHub: [https://github.com/huynguyennovem](https://github.com/huynguyennovem)
    
* LinkedIn: [https://linkedin.com/in/huynguyennovem](https://linkedin.com/in/huynguyennovem)