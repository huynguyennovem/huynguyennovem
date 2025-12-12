---
title: "Fix a Flutter framework issue from scratch"
seoTitle: "Fix a Flutter framework issue from scratch"
datePublished: Fri Dec 12 2025 10:45:44 GMT+0000 (Coordinated Universal Time)
cuid: cmj2qr63m000c02jp0bswe2qa
slug: fix-a-flutter-framework-issue-from-scratch
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1765535304981/236b5e04-fb21-4c61-afdc-0d2c2dd42b11.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1765536233254/3c57206e-b58b-4685-91c4-0ef12c8a8e86.png
tags: opensource, flutter, contribute

---

Hi, I’m Huy, GitHub handle [@huycozy](https://github.com/huycozy).  
I’m currently working as Flutter open-source engineer at [Codemagic](https://codemagic.io).  
So far, I’ve had [**41 PRs merged**](https://github.com/flutter/flutter/pulls?q=is%3Apr+author%3Ahuycozy+is%3Aclosed) into the Flutter repository.

Today, I want to share the journey of fixing a Flutter issue that stayed open for nearly **6 years**:  
👉 Issue [**#59143**](https://github.com/flutter/flutter/issues/59143) - *“****TabBar.image does not render at initialIndex for the first time****”*

In this article, I’ll walk through:

* Understanding the issue
    
* Setting up the environment
    
* Investigating the source code
    
* Implementing the fix
    
* Writing tests
    
* Solve CI failures
    
* Handling cross-team reviews
    
* Getting the PR merged
    

Let’s dive in!

# **Preparation**

Before diving into debugging and fixing the issue, you need a proper Flutter contributor setup.  
Here’s mine:

### **1\. Fork the repo**

Go to [https://github.com/flutter/flutter](https://github.com/flutter/flutter) and click **Fork** button.

### **2\. Clone your fork**

```console
git clone git@github.com:<your-github-handle-here>/flutter.git
```

For instance:

```console
git clone git@github.com:huycozy/flutter.git
```

### **3\. Add upstream remote**

Inside the repo directory:

```console
cd <your-forked-repo-location>
git remote -v
```

You should see something like:

```console
➜  flutter_pr git:(master) ✗ git remote -v
origin  git@github.com:huycozy/flutter.git (fetch)
origin  git@github.com:huycozy/flutter.git (push)
upstream        https://github.com/flutter/flutter.git (fetch)
upstream        https://github.com/flutter/flutter.git (push)
```

### **4\. Keep your fork updated**

Whenever you come back to contribute, fetch the latest code from upstream and rebase it onto local forked repo (assume you are on master branch/channel):

```console
git fetch upstream
git rebase upstream/master
```

(optional) Rebase onto your local master and push to your forked repo on GitHub:

```console
git rebase master
git push origin master -f
```

Your environment is ready. Now we begin the fun part: solving the bug.

# **Issue Analysis**

The issue title already gives a clue:

> **“TabBar.image does not render at initialIndex for the first time”**

Meaning:  
When a TabBar uses images (via `DecorationImage`), the initial tab’s image is not shown on first render - only on subsequent interactions.

The next steps of analysis:

### 1\. Read the issue thread

Always start by reading:

* The first comment (issue description)
    
* Minimal reproduction code
    
* other comments, confirmation, additional hints
    

Fortunately, the triage team provided a clean reproduction sample(thanks to [Triage](https://github.com/flutter/flutter/blob/master/docs/triage/README.md) process, I usually find triage comment first as triage team may refine the sample code so that you only need to copy-paste to reproduce the issue yourself locally). This is extremely valuable, as many issue reports come with complex code.

### **2\. Reproduce locally**

I tested sample code using the latest Flutter master branch and confirmed:

✔️ Bug still exists  
✔️ Happens consistently  
✔️ Only affects TabBar indicator images

### **3\. Study issue labels**

Key labels:

* `triaged-design` → A valid framework-level issue
    
* `P2` → Medium priority
    
* `workaround-available` → Some attempted workarounds exist
    

Some of the proposed “workarounds” (Timers, delayed animateTo, precacheImage, etc.) did **not** solve the issue. At this point, it’s clear that the root cause is internal to TabBar’s rendering system.

Next step: read the source code.

# Investigating the source code

TabBar is defined in[`packages/flutter/lib/src/material/tabs.dart`](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/material/tabs.dart)

While tracing the rendering flow, I found a reason why the issue occurs, which is related to "**indicator**" and "**image**". I shared the details at [https://github.com/flutter/flutter/issues/59143#issuecomment-3626318406](https://github.com/flutter/flutter/issues/59143#issuecomment-3626318406). I focused on two things related to the bug:

* **The indicator painter**
    
* **Asynchronous image loading**
    

Here’s what happens:

TabBar is using [\_indicatorPainter](https://github.com/flutter/flutter/blob/6ff4a4acd97d2d9906cd1bff274adba61716e66a/packages/flutter/lib/src/material/tabs.dart#L465), which is a CustomPainter to manage painting things in the whole bar, and each **image** on tabs is painted by a BoxPainter named `_painter`, which also takes image configuration:

* [tabs.dart#L571](https://github.com/flutter/flutter/blob/6ff4a4acd97d2d9906cd1bff274adba61716e66a/packages/flutter/lib/src/material/tabs.dart#L571)
    

```dart
_painter ??= indicator.createBoxPainter(markNeedsPaint);
```

* [tabs.dart#L582-L595](https://github.com/flutter/flutter/blob/6ff4a4acd97d2d9906cd1bff274adba61716e66a/packages/flutter/lib/src/material/tabs.dart#L582-L595)
    

```dart
    final configuration = ImageConfiguration(
      size: _currentRect!.size,
      textDirection: _currentTextDirection,
      devicePixelRatio: devicePixelRatio,
    );
    if (showDivider && dividerHeight! > 0) {
      final dividerPaint = Paint()
        ..color = dividerColor!
        ..strokeWidth = dividerHeight!;
      final dividerP1 = Offset(0, size.height - (dividerPaint.strokeWidth / 2));
      final dividerP2 = Offset(size.width, size.height - (dividerPaint.strokeWidth / 2));
      canvas.drawLine(dividerP1, dividerP2, dividerPaint);
    }
    _painter!.paint(canvas, _currentRect!.topLeft, configuration);
```

In this scenario, image is loaded asynchronously, so tracing the flow, we will see what happened inside as below:

1 . In \_indicatorPainter, it requests to create a boxpainter: [tabs.dart#L571](https://github.com/flutter/flutter/blob/6ff4a4acd97d2d9906cd1bff274adba61716e66a/packages/flutter/lib/src/material/tabs.dart#L571)

```dart
_painter ??= indicator.createBoxPainter(markNeedsPaint);
```

2. paint background image in BoxDecorationPainter: [box\_decoration.dart#L536-L540](https://github.com/flutter/flutter/blob/2343d68ca409b01fdb92057f6b24997aa92079f2/packages/flutter/lib/src/painting/box_decoration.dart#L536-L540)
    

```dart
  void _paintBackgroundImage(Canvas canvas, Rect rect, ImageConfiguration configuration) {
    if (_decoration.image == null) {
      return;
    }
    _imagePainter ??= _decoration.image!.createPainter(onChanged!);
```

3. handle asynchronous image source in DecorationImagePainter: [decoration\_image.dart#L375](https://github.com/flutter/flutter/blob/2343d68ca409b01fdb92057f6b24997aa92079f2/packages/flutter/lib/src/painting/decoration_image.dart#L375)
    

```dart
final listener = ImageStreamListener(_handleImage, onError: _details.onError);
```

Inside [\_handleImage](https://github.com/flutter/flutter/blob/2343d68ca409b01fdb92057f6b24997aa92079f2/packages/flutter/lib/src/painting/decoration_image.dart#L413), if it's a non-synchronousCall, `_onChanged` callback is called: [decoration\_image.dart#L423-L425](https://github.com/flutter/flutter/blob/2343d68ca409b01fdb92057f6b24997aa92079f2/packages/flutter/lib/src/painting/decoration_image.dart#L423-L425)

```dart
if (!synchronousCall) {
      _onChanged();
    }
```

`_onChanged` triggers `markNeedsPaint` callback, but in `markNeedsPaint`, it only sets `_needsPaint` flag to true, but doesn't actually trigger a repaint. I did debugging and saw that [shouldRepaint](https://github.com/flutter/flutter/blob/2343d68ca409b01fdb92057f6b24997aa92079f2/packages/flutter/lib/src/material/tabs.dart#L460) is not invoked, even though `_needsPaint` is true already.

# **Solution**

To fix the issue, we need to ensure the indicator painter **actually repaints** when the async image finishes loading.

There is already a mechanism for this in Flutter:

**CustomPainter accepts a** `repaint` Listenable: [custom\_paint.dart#L152-L153](https://github.com/flutter/flutter/blob/9d96df2364317334b55547ccb47c81aa9418eb71/packages/flutter/lib/src/rendering/custom_paint.dart#L152-L153)

```dart
/// The painter will repaint whenever `repaint` notifies its listeners.
  const CustomPainter({Listenable? repaint}) : _repaint = repaint;
```

The docs said:"*The painter will repaint whenever* `repaint` *notifies its listeners.*" So, we can leverage this.

The solution is to use a notifier, add it to repaint listener above, and trigger it on `markNeedsPaint` which is called when image indicator is loaded.

## Steps to fix

* Create a notifier:
    

```dart
// A ChangeNotifier for triggering repaints when async resources load.
class _IndicatorPainterNotifier extends ChangeNotifier {
  void notify() {
    notifyListeners();
  }
}
```

* Pass it to the CustomPainter:
    

```dart
_IndicatorPainter._({
....
    required _IndicatorPainterNotifier repaint,
  }) : _repaint = repaint,
       super(repaint: Listenable.merge(<Listenable?>[controller.animation, repaint]))
```

* Trigger repaint on image load:
    

```dart
  void markNeedsPaint() {
    _needsPaint = true;
    _repaint.notify();
  }

  void dispose() {
    assert(debugMaybeDispatchDisposed(this));
    _painter?.dispose();
    _repaint.dispose();
  }
```

With this, the indicator now correctly repaints when async images finish loading - including on the first frame.

# Manually test the fix

Use the original repro sample and run it with your locally built Flutter SDK (via alias, fvm, or switch scripts).

Result:  
✔️ Bug is gone  
✔️ Initial tab image displays correctly  
✔️ No regressions observed in manual UI tests

Now it's time to write automated tests.

# Writing tests

To verify repaint behavior, I used a custom decoration class that counts how many times the indicator is painted.

Key ideas:

* Initial paint count should be `1`
    
* After async load completes, paint count must increase
    
* Switching tabs should further increase paint count
    

```dart
testWidgets('TabBar indicator image should be rendered at initialIndex for the first time', (
    WidgetTester tester,
  ) async {
    // TabBar indicators with asynchronously loaded images (e.g. from network)
    // should trigger a repaint when the image finishes loading, even on the initial tab.
    final decoration = TabBarAsyncImageIndicatorDecoration();

    await tester.pumpWidget(
      MaterialApp(
        home: DefaultTabController(
          length: 3,
          child: Scaffold(
            appBar: AppBar(
              bottom: TabBar(
                indicator: decoration,
                tabs: const <Widget>[
                  Tab(text: 'One'),
                  Tab(text: 'Two'),
                  Tab(text: 'Three'),
                ],
              ),
            ),
            body: const TabBarView(
              children: <Widget>[
                Center(child: Text('Page One')),
                Center(child: Text('Page Two')),
                Center(child: Text('Page Three')),
              ],
            ),
          ),
        ),
      ),
    );

    // Initial paint - indicator should be painted once.
    expect(decoration.paintCount, 1);

    // Pump with duration to allow the event queue (async image load simulation) to complete.
    // Future.delayed(Duration.zero) schedules in the event queue, so we need to advance time.
    await tester.pump(const Duration(milliseconds: 1));

    // After async image loads, the indicator should be repainted.
    // This verifies that the markNeedsPaint callback properly triggers a repaint.
    expect(
      decoration.paintCount,
      greaterThan(1),
      reason: 'Indicator should be repainted after async image loads',
    );

    // Verify the indicator repaints when switching tabs.
    final int initialPaintCount = decoration.paintCount;
    await tester.tap(find.text('Two'));
    await tester.pumpAndSettle();

    expect(
      decoration.paintCount,
      greaterThan(initialPaintCount),
      reason: 'Indicator should repaint when switching tabs',
    );
  });
```

Run the test (also existing tests to make sure your fix doesn't introduce and regression issue)

# **Submitting the fix**

1. ### **Create a branch**
    

```bash
git checkout -b fix/tabbar-image-repaint
```

2. ### Commit & push
    

```bash
git add .
git commit -m "Fix TabBar indicator image not rendering on initialIndex"
git push origin fix/tabbar-image-repaint
```

GitHub will automatically prompt you to open a PR.

3. ### Filling PR template
    

You need to follow PR template, fill up all the information so that reviewers can understand your fix better. Make sure you read the checklist too. Once you have completed filling PR description, just click the green button at the bottom to create the PR it is usually in draft state. I also prefer it as I will wait CI check to complete before marking it ready for review.

* Explain the root cause
    
* Describe your fix
    
* Link to issue #59143 (make sure to use keywords to close issues automatically once the PR gets merged, see [**Linking a pull request to an issue**](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/using-keywords-in-issues-and-pull-requests#linking-a-pull-request-to-an-issue)**)**
    
* Demo the fix with screenshot or video
    
    For example of mine:
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1765534007463/20bd8cae-eac3-4872-a0f6-cddbe6d34182.png align="center")

# Solve CI tests

You may encounter failed tests from CI checks:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1765534185124/b8d34b60-34e8-48c8-99c3-11e698a1b86e.png align="center")

This is how I find the detailed error from the output (watch this [video](https://www.youtube.com/watch?v=0Pb1P0bEblE) for another example):

[https://youtu.be/-JMYvEvxlsc](https://youtu.be/-JMYvEvxlsc)

Oh wow, it failed at [flutter/dev/integration\_tests/flutter\_gallery/test/smoke\_test.dart](https://github.com/flutter/flutter/blob/master/dev/integration_tests/flutter_gallery/test/smoke_test.dart) . Now, open your IDE with the forked repo again, find that tests and try to reproduce the failure locally. Ok, this seems to be an easy fix, as it's saying that the new class `_IndicatorPainterNotifier` is missing `toString()` method.

What should I do now? Just do the search in the entire project with keywords:"*extends ChangeNotifier {*". Then you will see there are a lot of classes having toString() already. I found one `_InputBorderGap extends ChangeNotifier {`. I just need to override the same:

```dart
@override
  String toString() => describeIdentity(this);
```

Then run `smoke_test.dart` again, voila, it's passed now! Now you need to submit the fix to re-trigger the CI. Since the pr is only in draft, not ready to review yet, I will do git ***amend*** commit(or even squash) so that my commit history will be clean for reviewers.

Once you push the commit, CI tasks will re-run automatically. Now just need to wait for them to finish. Wo ho, all tests are passed now!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1765534710494/a5c30946-1d3c-4b68-be9e-ed8fb9900025.png align="center")

It’s ready to get reviews from others. In the reviewers box, it will suggest some reviewers who have usually contributed to the changed files before.

…&lt;to-be-continued&gt;

(I will update the article later once the PR gets reviews and is merged)