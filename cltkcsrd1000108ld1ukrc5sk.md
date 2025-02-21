---
title: "Bisecting regression issue in Flutter"
seoTitle: "Bisecting regression framework issue in Flutter"
datePublished: Sat Mar 09 2024 17:22:34 GMT+0000 (Coordinated Universal Time)
cuid: cltkcsrd1000108ld1ukrc5sk
slug: bisecting-regression-issue-in-flutter
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724421083361/3b297038-c29b-4211-a819-172940388cca.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724421095862/82eaf5b8-4795-47b9-9ff5-6dbe6134da20.jpeg
tags: framework, flutter, git, git-bisect

---

Flutter is a popular framework for building cross-platform applications. However, regression issues may arise occasionally due to the introduction of a bad commit. Identifying and isolating these problematic commits is crucial for maintaining a stable framework. In this blog post, I will explore the process of bisecting a bad commit in a regression issue. I will also provide some helpful tips for effective bisecting.

### Start bisecting a regression issue

This is a regression issue filed in Flutter repository: [https://github.com/flutter/flutter/issues/129969](https://github.com/flutter/flutter/issues/129969). It's a framework issue that causes application to crash when dragging mouse over Tooltip while selecting text in SelectionArea.

Let's assume you have already had multiple Flutter versions installed on your machine (you can use fvm or alias). I'm using alias and have stable, beta, master channels on my machine corresponding to *flutter*, *flutterb* and *flutterm* aliases.

First, we need to identify which channel we should start doing bisect. In this case, I can verify quickly the issue is not reproducible on stable channel, but it's reproducible on master channel (by running sample code on both these channels). So we will start bisecting on master channel.

The first good commit will be the commit on which version 3.10.5 is released. You can check this commit on the Flutter release page at [https://docs.flutter.dev/release/archive?tab=macos](https://docs.flutter.dev/release/archive?tab=macos). After looking version 3.10.5 on release page, I can see the good commit is `796c8ef`.

Let's start!

**Step 1: cd to Flutter SDK path and mark commit range**

```bash
➜  cd <your-flutter-master-sdk>
➜  flutter_master git:(master) git bisect start
➜  flutter_master git:(master) git bisect bad              //the latest commit is bad
➜  flutter_master git:(master) git bisect good 796c8ef     //the commit on 3.10.5 release is good
Bisecting: a merge base must be tested
[e749db6f915c341d15b9cb81dc19bdc9e791c72f] Revert "Refactor reorderable list semantics" (#124368)
```

**Step 2: Evaluate commit is good or bad**

We notice git revision has been changed to `e749db6f91`. Git automatically check out a commit for testing. Then we need to open project where you found the bug, run it and confirm whether the bug is present or not. Make sure you select flutter master channel for that project.

If the result is good, let's mark that commit as good. Otherwise, let's mark it as bad. In this case, I can see the bug is not present on this commit, so I will mark it as good.

```bash
➜  flutter_master git:(e749db6f91) git bisect good        
Bisecting: 662 revisions left to test after this (roughly 9 steps)
[8135a4bedac87a8fc61318f1848f7acc9a511964] Roll Flutter Engine from 3c23ddae1d2a to 9039c2dfb74c (2 revisions) (#127154)
```

**Step 3: Repeat testing and marking**

Git will automatically select a new commit for testing based on your previous marking. And then it will display the final commit hash associated with the bad commit.

```bash
➜  flutter_master git:(8135a4beda) git bisect good 
Bisecting: 331 revisions left to test after this (roughly 8 steps)
[ddfe8a358cbcff2f72af3b382d8a625a239068cf] Update `chip.dart` to use set of `MaterialState` (#128507)
➜  flutter_master git:(ddfe8a358c) git bisect good 
Bisecting: 165 revisions left to test after this (roughly 7 steps)
[5d8cc978b9113bf2a6033131e67b030f0ebb93d2] Refactor `Analytics` global getter to point to context only (#129196)
➜  flutter_master git:(5d8cc978b9) git bisect bad  
Bisecting: 82 revisions left to test after this (roughly 6 steps)
[4fe84d7af72fe04fad20b40e62eb9973fda8245f] Roll Flutter Engine from 66a5761412f9 to 727b4413fe6f (10 revisions) (#128841)
➜  flutter_master git:(4fe84d7af7) git bisect bad
Bisecting: 41 revisions left to test after this (roughly 5 steps)
[6c471d3ec24b5fc0d3f9004d61d9be85322762bd] Roll Flutter Engine from 962d78e0ae9c to 3d76ba6d6d5f (1 revision) (#128645)
➜  flutter_master git:(6c471d3ec2) git bisect bad
Bisecting: 20 revisions left to test after this (roughly 4 steps)
[befd86d6b14e1df2f4a10d9e282970a6ffa978d7] Roll Flutter Engine from cb93477008d6 to 93afba901b3b (2 revisions) (#128573)
➜  flutter_master git:(befd86d6b1) git bisect good
Bisecting: 10 revisions left to test after this (roughly 3 steps)
[a35ae60102e77eb6d2556e95d5061823063209ab] Roll Flutter Engine from bc6e047570f6 to 071e1fb21c7a (1 revision) (#128602)
➜  flutter_master git:(a35ae60102) git bisect good
Bisecting: 5 revisions left to test after this (roughly 3 steps)
[ca11492b6bdc0f49708173324dccc461fc20e1d9] Roll Flutter Engine from 488876ed26c6 to 3e90345cdca7 (3 revisions) (#128617)
➜  flutter_master git:(ca11492b6b) git bisect good
Bisecting: 2 revisions left to test after this (roughly 2 steps)
[e39ed8e86aa18192d272a0d9ca345a0240c8ccf8] Bump actions/checkout from 3.5.2 to 3.5.3 (#128625)
➜  flutter_master git:(e39ed8e86a) git bisect good
Bisecting: 0 revisions left to test after this (roughly 1 step)
[e1fdb1aa74003f771eb83ba0e9828a04ce7799f4] migrate `Tooltip` to use  `OverlayPortal` (#127728)
➜  flutter_master git:(e1fdb1aa74) git bisect bad 
Bisecting: 0 revisions left to test after this (roughly 0 steps)
[9e8143a0473ec9f3f640cd199d0d393b8f9324ca] Roll Flutter Engine from b037db26037f to 962d78e0ae9c (10 revisions) (#128643)
➜  flutter_master git:(9e8143a047) git bisect good
e1fdb1aa74003f771eb83ba0e9828a04ce7799f4 is the first bad commit
commit e1fdb1aa74003f771eb83ba0e9828a04ce7799f4
Author: LongCatIsLooong <31859944+LongCatIsLooong@users.noreply.github.com>
Date:   Sat Jun 10 13:54:37 2023 -0700

    migrate `Tooltip` to use  `OverlayPortal` (#127728)
    
    https://github.com/flutter/flutter/issues/7151 isn't a problem with OverlayPortal so the test is removed.
    Also removed some `mounted` checks since they're no longer needed.

 packages/flutter/lib/src/material/tooltip.dart   | 166 ++++++++---------------
 packages/flutter/test/material/tooltip_test.dart |  41 +-----
 2 files changed, 61 insertions(+), 146 deletions(-)
➜  flutter_master git:(9e8143a047)
```

Great! Now you found the bad commit and the PR caused the bug. You can post it to issue thread and wait for the fix. This will help issue-fixing progress faster.

Don't forget to reset bisecting process after you found the bad commit.

```bash
git bisect reset
flutter doctor  //to fetch SDK again
```

### Tips for Bisecting

1. Choose the right Flutter version to start bisecting. When identifying a known good and bad commit, it is essential to choose versions that are reasonably close to each other. This ensures a more efficient bisecting process, as commits too far apart may require excessive testing and marking. Let's store several stable versions on your machine and check them manually before bisecting, to determine a stable version contains good commits.
    
2. You may see this message each time run the app again:
    

```bash
Waiting for another flutter command to release the startup lock...
```

It will take a bit (a few tens of seconds or longer depending on your machine), but be patient. Open Activity Monitor and monitor `dart`/`dart:flutter_tools:snapshot` processes, if they take up too many resources and cause freezing (you see the above message for too long without any progress (app can't run), kill them and run flutter again (this case rarely happens and I hardly need to use it).

### Conclusion

Bisecting bad commits is an essential technique for diagnosing regression issues in the Flutter framework. By following the outlined steps and utilizing the provided tips, you can efficiently isolate the commit responsible for the issue and contribute to improving the stability and reliability of Flutter applications. Remember to share your findings and contribute to the Flutter issue database, enabling the community to resolve these problems collaboratively.

Happy bisecting!

Stay in touch with me for updates:

* Twitter: [https://twitter.com/HuyNguyenTw](https://twitter.com/HuyNguyenTw)
    
* GitHub: [https://github.com/huynguyennovem](https://github.com/huynguyennovem)
    
* LinkedIn: [https://linkedin.com/in/huynguyennovem](https://linkedin.com/in/huynguyennovem)