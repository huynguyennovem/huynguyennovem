---
title: "Resolving "Xcode issue UnityAds.h file not found" error when building iOS on Unity"
datePublished: Wed Nov 19 2025 12:08:43 GMT+0000 (Coordinated Universal Time)
cuid: cmi5ylac6000402jvd0k73swp
slug: resolving-xcode-issue-unityadsh-file-not-found-error-when-building-ios-on-unity
tags: unity, ios

---

I tried this way and it seems to work for me:

1. Create pod: cd to the generated iOS project, `pod init`, then add pod UnityAds, follow this [https://docs.unity.com/en-us/grow/ads/ios-sdk/install-sdk](https://docs.unity.com/en-us/grow/ads/ios-sdk/install-sdk)
    

```plaintext
target 'Unity-iPhone' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for Unity-iPhone
  pod 'UnityAds'

  target 'Unity-iPhone Tests' do
    inherit! :search_paths
    # Pods for testing
  end
```

2. run `pod install`
    
3. On XCode, go to Frameworks, Libraries and Embedded Content, add `UnityAds.xcframework`
    
4. Reopen XCode with `.xcworkspace` file. It should appear after installing a plugin
    
5. Product &gt; Archive again