---
title: "11 manual tests before publishing your Flutter app"
seoTitle: "11 manual tests before publishing your Flutter app"
datePublished: Thu Aug 07 2025 04:28:05 GMT+0000 (Coordinated Universal Time)
cuid: cme0wcc19001e02js56qpgq4a
slug: 11-manual-tests-before-publishing-your-flutter-app
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1761800240281/578b3ea9-6fcf-4cc4-8924-6d4d5d01cc08.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1754540800867/cbe3282b-f86b-4103-8e1e-d051472b7dbc.png
tags: app-development, flutter, testing, publishing

---

1. **Run** `flutter analyze`, `flutter run -v`, and go through the output logs.  
    This helps you catch unresolved warnings in the code and also check if you’ve accidentally printed any sensitive information to the console.
    
2. **Run** `flutter run --profile`.  
    Use Flutter DevTools to monitor your app’s performance. Watch out for screens with too many janks or overloaded raster threads. These might indicate issues like loading large images, performing heavy tasks on the main thread, or improperly optimized scrollable lists.
    
3. **Run** `flutter run --release`.  
    This allows you to check whether the release build is smaller (IPA, AAB/APK) due to tree shaking, and whether startup time and overall performance improve. Most importantly, confirm that the app doesn't crash. Some bugs only appear in release mode, such as assertion errors, dynamic library loading issues on Android, viewport dimension problems, etc.
    
4. **Test on all available devices and OS versions, if any.**  
    This helps detect compatibility issues you might miss when developing on a particular machine. Consider using [Firebase Test Lab](https://firebase.google.com/docs/test-lab) for more comprehensive testing.
    
5. **If your budget allows, get at least one Android device from each major Chinese OEM (e.g: Xiaomi, Huawei) and a Samsung Galaxy.**  
    Impeller-related bugs (text rendering, images, scrolling) and even fatal crashes are commonly found on these devices. Although most of them get fixed quickly, there’s no guarantee that all edge cases are covered before they hit production.
    
6. **Integrate an analytics tool to catch crashes early.**  
    Tools like Firebase Analytics or Sentry can help detect crashes that don’t appear in release mode, beta/closed testing (Android), or TestFlight (iOS), but only emerge in production.
    
7. **Test app behavior with OS-specific gestures and settings:**  
    Examples include gesture-based navigation (edge-swipe back) on both Android and iOS, and scroll-to-top gestures for list views on iOS.
    
8. **Test foreground and background switching:**  
    For example, use the app, then go to the home screen or recent apps menu and return. Ensure the app handles these transitions smoothly.
    
9. **Accessibility (a11y) testing:**  
    Enable TalkBack (Android) and VoiceOver (iOS). Check whether the main features of the app are usable and whether screen readers correctly read all labels. Imagine using the app as someone with visual or auditory impairments.
    
10. **Test with Android Developer Options:**  
    Enable **"Don’t keep activities"** and adjust **"Transition animation scale"** settings to test how your app handles system-level interruptions and UI animations.
    
11. **Test under poor network conditions:**  
    On Android, use the emulator’s built-in network throttling. On iOS, use **Network Link Conditioner** available in Developer settings.
    

Happy testing!