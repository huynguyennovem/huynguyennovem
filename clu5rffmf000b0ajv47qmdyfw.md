---
title: "Compiling Flutter Engine Locally - Web engine (Part III)"
seoTitle: "Compiling Flutter engine"
datePublished: Sun Mar 24 2024 16:55:16 GMT+0000 (Coordinated Universal Time)
cuid: clu5rffmf000b0ajv47qmdyfw
slug: compiling-flutter-engine-locally-web-engine-part-iii
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724419536782/b936c412-2dc3-4375-90cd-379f5efd29bb.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724419549008/e97f3668-168d-43a7-adcf-4dfd3fdb928b.jpeg
tags: tutorial, flutter, engine

---

In this part, we will try building Web engine and also handle some errors that may happen during the build. We will use `felt` tool for this. Before using it, let's add it to your PATH environment variables. The path would be: `ENGINE_ROOT/src/flutter/lib/web_ui/dev`. For e.g:

```bash
export PATH=/Users/huynq/Documents/GitHub/engine/src/flutter/lib/web_ui/dev:$PATH
```

Then you can call `felt` command from anywhere.

Next, as common steps stated in previous tutorials, we need to check out engine commit we want to build with and also update dependencies. In this tutorial, I assume we use the latest engine revision from main branch.

1. Update the Flutter Engine repo:
    

```bash
➜  cd /Users/huynq/Documents/GitHub/engine/src/flutter
➜  git pull upstream main
```

2. Update dependencies
    

```bash
➜  engine gclient sync
Syncing projects: 100% (137/137), done.                                                                            
Hook 'python3 src/flutter/tools/pub_get_offline.py' took 14.53 secs
Running hooks: 100% (14/14), done.
```

3. Felt build
    

This command will build Web engine target. Some targets could be used with: `sdk`, `canvaskit`, `canvaskit_chromium` and `skwasm`. You can check them all [here](https://github.com/flutter/engine/blob/main/lib/web_ui/README.md#felt-build).

```bash
➜  src git:(44ca359) felt build
Running on MacOS. Will check the file and user limits.
File limits too low increasing the file limits
User limits too low increasing the user limits
Running `dart pub get` in 'engine/src/flutter/lib/web_ui'
Running gn...
Generating GN files in: out/wasm_release
Generating compile_commands took 39ms
Done. Made 185 targets from 94 files in 3169ms
Running autoninja...
ninja: Entering directory `/Users/huynq/Documents/GitHub/engine/src/out/wasm_release'
[139/2539] LINK canvaskit/canvaskit.js
cache:INFO: generating system asset: symbol_lists/266569fa9c69a946597f128c95d4d54feb22c5ff.json... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/symbol_lists/266569fa9c69a946597f128c95d4d54feb22c5ff.json" for subsequent builds)
cache:INFO:  - ok
[2530/2539] LINK skwasm/skwasm.js
cache:INFO: generating system asset: symbol_lists/8332cd38f4026705eee5a4cdaee53cba8dad116b.json... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/symbol_lists/8332cd38f4026705eee5a4cdaee53cba8dad116b.json" for subsequent builds)
cache:INFO:  - ok
cache:INFO: generating system asset: sysroot/lib/wasm32-emscripten/lto/crtbegin.o... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/crtbegin.o" for subsequent builds)
system_libs:INFO: compiled 1 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libGL-mt-webgl2.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libGL-mt-webgl2.a" for subsequent builds)
system_libs:INFO: compiled 4 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libc_optz-mt.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libc_optz-mt.a" for subsequent builds)
system_libs:INFO: compiled 7 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libbulkmemory.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libbulkmemory.a" for subsequent builds)
system_libs:INFO: compiled 4 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libnoexit.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libnoexit.a" for subsequent builds)
system_libs:INFO: compiled 1 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libc-mt.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libc-mt.a" for subsequent builds)
system_libs:INFO: compiled 1108 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libdlmalloc-mt.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libdlmalloc-mt.a" for subsequent builds)
system_libs:INFO: compiled 1 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libcompiler_rt-mt.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libcompiler_rt-mt.a" for subsequent builds)
system_libs:INFO: compiled 175 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libc++-mt-noexcept.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libc++-mt-noexcept.a" for subsequent builds)
system_libs:INFO: compiled 49 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libc++abi-mt-noexcept.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libc++abi-mt-noexcept.a" for subsequent builds)
system_libs:INFO: compiled 16 inputs
cache:INFO:  - ok
cache:INFO: generating system library: sysroot/lib/wasm32-emscripten/lto/libsockets-mt.a... (this will be cached in "/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/cache/sysroot/lib/wasm32-emscripten/lto/libsockets-mt.a" for subsequent builds)
system_libs:INFO: compiled 21 inputs
cache:INFO:  - ok
[2539/2539] STAMP obj/default.stamp
➜  src git:(44ca359)
```

4. Run your Flutter project
    

```bash
➜  flutterm create demo_web_engine
➜  cd demo_web_engine
➜  demo_web_engine flutterm --local-web-sdk=wasm_release run -d chrome --local-engine-src-path=/Users/huynq/Documents/GitHub/engine/src
Launching lib/main.dart on Chrome in debug mode...
Warning: In index.html:37: Local variable for "serviceWorkerVersion" is deprecated. Use "{{flutter_service_worker_version}}" template token instead.
Warning: In index.html:46: "FlutterLoader.loadEntrypoint" is deprecated. Use "FlutterLoader.load" instead.
Waiting for connection from debug service on Chrome...             22.8s
This app is linked to the debug service: ws://127.0.0.1:58589/P1bktcnKBgw=/ws
Debug service listening on ws://127.0.0.1:58589/P1bktcnKBgw=/ws

🔥  To hot restart changes while running, press "r" or "R".
For a more detailed help message, press "h". To quit, press "q".

A Dart VM Service on Chrome is available at: http://127.0.0.1:58589/P1bktcnKBgw=
The Flutter DevTools debugger and profiler on Chrome is available at: http://127.0.0.1:9100?uri=http://127.0.0.1:58589/P1bktcnKBgw=
```

### Troubleshooting

1. felt build failed: `buildtools/emsdk/upstream/emscripten/em++: No such file or directory`
    

```bash
➜  src git:(44ca359) felt build
Running on MacOS. Will check the file and user limits.
File limits too low increasing the file limits
Password:
User limits too low increasing the user limits
Running `dart pub get` in 'engine/src/flutter/lib/web_ui'
Running gn...
Generating GN files in: out/wasm_release
Generating compile_commands took 44ms
Done. Made 185 targets from 94 files in 3670ms
Running autoninja...
ninja: Entering directory `/Users/huynq/Documents/GitHub/engine/src/out/wasm_release'
[7/4197] CXX canvaskit/obj/flutter/third_party/skia/src/ports/fontmgr_custom.SkFontMgr_custom.o
FAILED: canvaskit/obj/flutter/third_party/skia/src/ports/fontmgr_custom.SkFontMgr_custom.o 
/Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/em++ --em-config /Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/.emscripten -MD -MF canvaskit/obj/flutter/third_party/skia/src/ports/fontmgr_custom.SkFontMgr_custom.o.d -DUSE_OPENSSL=1 -D_LIBCPP_ENABLE_THREAD_SAFETY_ANNOTATIONS -DNDEBUG -DNVALGRIND -DDYNAMIC_ANNOTATIONS_ENABLED=0 -DSK_ENABLE_DUMP_GPU -DSK_FORCE_AAA -DSK_LEGACY_IGNORE_DRAW_VERTICES_BLEND_WITH_NO_SHADER -DSK_DISABLE_LEGACY_GRDIRECTCONTEXT_BOOLS -DSK_DISABLE_LEGACY_GRDIRECTCONTEXT_FLUSH -DSK_RESOLVE_FILTERS_BEFORE_RESTORE -DSK_DISABLE_LEGACY_SHADERCONTEXT -DSK_DISABLE_LOWP_RASTER_PIPELINE -DSK_FORCE_RASTER_PIPELINE_BLITTER -DSK_METAL_WAIT_UNTIL_SCHEDULED -DSK_DISABLE_EFFECT_DESERIALIZATION -DSK_ENABLE_PRECOMPILE -DSKNX_NO_SIMD -DSK_FORCE_8_BYTE_ALIGNMENT -DSK_DISABLE_TRACING -DSK_ASSUME_WEBGL=1 -DSK_USE_WEBGL -DSK_GANESH -DSK_GAMMA_APPLY_TO_A8 -DSKIA_IMPLEMENTATION=1 -DSK_TYPEFACE_FACTORY_FREETYPE -I../.. -Icanvaskit/gen -I../../flutter/third_party/skia -fno-strict-aliasing -fcolor-diagnostics -Wendif-labels -Werror -Wno-missing-field-initializers -Wno-unused-parameter -Wno-shift-negative-value -Wno-unused-function -Wno-unused-variable -Wno-unused-but-set-parameter -Wno-unused-but-set-variable -Wno-implicit-int-float-conversion -Wno-deprecated-copy -Wno-psabi -Wno-deprecated-literal-operator -Wno-non-c-typedef-for-linkage -Wno-range-loop-construct -Wstring-conversion -Wnewline-eof -O3 -g0 -fvisibility-inlines-hidden -std=c++17 -fno-rtti   -c ../../flutter/third_party/skia/src/ports/SkFontMgr_custom.cpp -o canvaskit/obj/flutter/third_party/skia/src/ports/fontmgr_custom.SkFontMgr_custom.o
/bin/sh: /Users/huynq/Documents/GitHub/engine/src/buildtools/emsdk/upstream/emscripten/em++: No such file or directory
[16/4197] ACTION //flutter/web_sdk:web_engine_library(//build/toolchain/wasm:wasm)
ninja: build stopped: subcommand failed.
Pipeline experienced the following failures:
  "ninja": Sub-process failed.
Command: autoninja -C /Users/huynq/Documents/GitHub/engine/src/out/wasm_release
Working directory: /Users/huynq/Documents/GitHub/engine/src/flutter/lib/web_ui
Exit code: 1

Test pipeline failed.
```

It's telling `emsdk` is missing from `buildtools` dir. You need to add it manually:

* Step 1: Add `download_emsdk` to `engine/.gclient` file as below:
    

```bash
solutions = [
  {
    "managed": False,
    "name": "src/flutter",
    "url": "git@github.com:huycozy/engine.git",
    "custom_deps": {},
    "deps_file": "DEPS",
    "safesync_url": "",
    "custom_vars": {"download_emsdk": True},
  },
]
```

* Step 2: Update dependencies again
    

```bash
➜  engine gclient sync
```

Then you will see `emsdk` is downloaded in `buildtools`.

2. `web` dependency error
    

You may hit errors like:

```dart
               ^
../../.pub-cache/hosted/pub.dev/web-0.5.1/lib/src/dom/webrtc_encoded_transform.dart:148:34: Error: Type 'invalid-type' is not a valid type for external `dart:js_interop` APIs. The only valid types are: @staticInterop types, JS types from `dart:js_interop`, void, bool, num, double, int, String, and any extension type that erases to one of these types.
Use one of the valid types instead.
  external JSArray<JSNumber> get contributingSources;
                                 ^
../../.pub-cache/hosted/pub.dev/web-0.5.1/lib/src/dom/webrtc_encoded_transform.dart:216:32: Error: Type 'invalid-type' is not a valid type for external `dart:js_interop` APIs. The only valid types are: @staticInterop types, JS types from `dart:js_interop`, void, bool, num, double, int, String, and any extension type that erases to one of these types.
Use one of the valid types instead.
  external JSPromise<JSNumber> generateKeyFrame([String rid]);
                               ^
../../.pub-cache/hosted/pub.dev/web-0.5.1/lib/src/dom/webrtc_encoded_transform.dart:237:30: Error: Type 'invalid-type' is not a valid type for external `dart:js_interop` APIs. The only valid types are: @staticInterop types, JS types from `dart:js_interop`, void, bool, num, double, int, String, and any extension type that erases to one of these types.
Use one of the valid types instead.
  external JSPromise<JSAny?> sendKeyFrameRequest();
                             ^
../../.pub-cache/hosted/pub.dev/web-0.5.1/lib/src/dom/webrtc_encoded_transform.dart:252:20: Error: Type 'invalid-type' is not a valid type for external `dart:js_interop` APIs. The only valid types are: @staticInterop types, JS types from `dart:js_interop`, void, bool, num, double, int, String, and any extension type that erases to one of these types.
Use one of the valid types instead.
  external factory RTCRtpScriptTransform(
                   ^
../../.pub-cache/hosted/pub.dev/web-0.5.1/lib/src/dom/xhr.dart:263:40: Error: Type 'invalid-type' is not a valid type for external `dart:js_interop` APIs. The only valid types are: @staticInterop types, JS types from `dart:js_interop`, void, bool, num, double, int, String, and any extension type that erases to one of these types.
Use one of the valid types instead.
  external JSArray<FormDataEntryValue> getAll(String name);
                                       ^
Failed to compile application.
```

It could be `web` package version depreciation issue. Just try downgrading package version then retry.

```bash
dart pub downgrade web
```

Or use a specific package version in project:

```yaml
dependencies:
  flutter:
    sdk: flutter
  web: 0.4.2
```

Hope this will be useful for you!

Stay in touch with me for updates:

* Twitter: [https://twitter.com/HuyNguyenTw](https://twitter.com/HuyNguyenTw)
    
* GitHub: [https://github.com/huynguyennovem](https://github.com/huynguyennovem)
    
* LinkedIn: [https://linkedin.com/in/huynguyennovem](https://linkedin.com/in/huynguyennovem)
    

### References

* [https://github.com/flutter/flutter/wiki/Compiling-the-engine#compiling-for-the-web](https://github.com/flutter/flutter/wiki/Compiling-the-engine#compiling-for-the-web)
    
* [https://github.com/flutter/engine/blob/main/lib/web\_ui/README.md](https://github.com/flutter/engine/blob/main/lib/web_ui/README.md)