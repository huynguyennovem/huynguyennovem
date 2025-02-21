---
title: "Releasing Flutter projects on GitHub"
seoTitle: "Releasing Flutter projects on GitHub"
datePublished: Sat Mar 09 2024 17:13:02 GMT+0000 (Coordinated Universal Time)
cuid: cltkcghnx000008l2enkt0qwc
slug: releasing-flutter-projects-on-github
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724419736077/fc906af2-4c26-498a-a1a3-b234ea3972a6.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724419762879/7a8aa065-11f2-4335-8deb-95da479c58d9.webp
tags: github, opensource, flutter

---

Flutter is a popular open-source framework for building high-performance, high-fidelity mobile, web, and desktop applications. If you’re working on a Flutter project and want to distribute it on GitHub, this article will guide you through the process.

In this article, I will use [NetShare](https://github.com/huynguyennovem/netshare) as an example project. NetShare is an open-source Flutter project that makes it easy to share data in a local network. The latest release includes support for Android, macOS, Windows, and Linux platforms.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710004291830/786b8432-5881-4540-af9d-83a6fb7441c7.png align="center")

### Android

1. Prepare the app icon by using your preferred design tool. You can use Adobe Illustrator to export a high-quality PNG icon and then use Android Studio to generate all density icons. For more information, see the official [Create App Icons](https://developer.android.com/studio/write/create-app-icons#access) documentation.
    
2. Open `android` project (inside `flutter` project) on Android Studio and add the app icon.
    
3. Set up the release configuration/key per the guideline provided in the official [Flutter documentation](https://docs.flutter.dev/deployment/android).
    
4. Build the release APK from the console on the Flutter project root directory: `flutter build apk`
    
5. Upload the release APK to the Release on your GitHub project.
    

### macOS

1. Design your app icon by using your preferred design tool. And then you may use [appicon.co](http://appicon.co) to generate all density icons.
    
2. Open the macOS project and update the AppIcon on XCode. Check official [Apple documentation](https://developer.apple.com/documentation/xcode/configuring-your-app-icon) for more details.
    
3. Build the project from the console on the Flutter project root directory: `flutter build macos`. Sometimes, the old icon may be cached, and you need to run \`flutter clean before building the app.
    
4. On XCode, go to `Product > Archive`.
    
5. Once the archive is finished, click on Distribute App.
    
6. Select the method of distribution as `Copy App`
    
7. Compress the app to a zip file and upload it to Release on your GitHub project.
    

### Windows

1. Update the app icon at `windows\runner\resources\app_icon.ico`
    
2. Change the app name at `windows\runner\main.cpp`
    
3. Execute `flutter build windows`
    
4. Download and install [Inno Setup](https://jrsoftware.org/download.php/is.exe)
    
5. Open Inno Setup, create a new app, and follow step-by-step with [protocoderspoint’s tutorial](https://protocoderspoint.com/how-to-create-exe-installation-file-of-flutter-windows-application/)
    
6. Upload the output file to the Release on your GitHub project.
    

You may also want to try the MSIX distribution by following the [official Flutter guideline](https://docs.flutter.dev/platform-integration/windows/building#distributing-windows-apps).

### Linux

Flutter apps can also be distributed on Linux using the Debian package format. To create a Debian package, we can use the `flutter_to_debian` package.

1. Follow the instructions in the `flutter_to_debian` package documentation at [https://pub.dev/packages/flutter\_to\_debian](https://pub.dev/packages/flutter_to_debian).
    
2. Add `flutter_to_debian` to your system PATH by running the following command in your terminal:
    

```bash
export PATH="$PATH":"$HOME/.pub-cache/bin"
```

3. Run `flutter_to_debian` from your Flutter project directory
    

```bash
huynq@ubuntu:~/Documents/WIP/netshare$ flutter_to_debian

checking for debian 📦 in root project...  ✅

start building debian package... ♻️  ♻️  ♻️

No skeleton found
🔥🔥🔥 (debian 📦) build done successfully  ✅

😎 find your .deb at
debian/packages/NetShare_2.0.0_amd64.deb
```

4. Test the output by installing it locally using the `dpkg` command:
    

```bash
sudo dpkg -i NetShare_2.0.0_amd64.deb
```

5. If the installation is successful and the app works as expected, upload output file to Releases on your GitHub project.
    

### iOS

iOS distribution requires an Apple developer account. Instead, publishing app to the AppStore could be an option. Please see the guidelines at [Build and release an iOS app](https://docs.flutter.dev/deployment/ios) for specific steps.

### Web

Web is easy to distribute since there are various ways to do it. You could use Firebase hosting or other hosting services to host built web content.

1. Execute `flutter build web`. There are two renderers `html` and `canvaskit` that you can choose (see [web renderers](https://docs.flutter.dev/platform-integration/web/renderers) for more details).
    
2. Check the output result locally with your local web server (node/python/…). I use Python server as an example:
    

```bash
cd build/web
python -m http.server 8000
```

This will start a web server at [http://localhost:8000](http://localhost:8000) that serves the output files in the `build/web` directory.

3. Deploy web by your favorite service (see [deploy-to-the-web](https://docs.flutter.dev/deployment/web#deploying-to-the-web)).
    

### Conclusion

In this article, we have walked through the steps of releasing a Flutter project on GitHub. Each platform requires specific configuration and tooling, but with the appropriate guidelines and references, you can distribute your app on any platform you choose. By following these guidelines and distributing your Flutter project on GitHub, you can reach a wider audience and make your project accessible to others.

Thank you for reading this article, and I hope that it will be helpful in guiding you through the process of releasing your Flutter project on GitHub.

Also, if you are interested in the NetShare project, please keep up with the progress by following project on Github and Twitter:

* Github project: [https://github.com/huynguyennovem/netshare](https://github.com/huynguyennovem/netshare)
    
* Twitter: [https://twitter.com/NetShareOSS](https://twitter.com/NetShareOSS)
    
* Maintainer: [https://twitter.com/HuyNguyenTw](https://twitter.com/HuyNguyenTw)
    

Thanks for reading!