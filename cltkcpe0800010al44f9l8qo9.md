---
title: "Crafting responsive Desktop UI for OverlayEntry in Flutter"
seoTitle: "Crafting responsive Desktop UI for OverlayEntry in Flutter"
datePublished: Sat Mar 09 2024 17:19:57 GMT+0000 (Coordinated Universal Time)
cuid: cltkcpe0800010al44f9l8qo9
slug: crafting-responsive-desktop-ui-for-overlayentry-in-flutter
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724421188208/6a4b2fa7-8d63-4d86-a328-1f8a5c013cf5.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724421177505/246f2907-7951-47e0-9964-98da2df71b77.jpeg
tags: tutorial, desktop, flutter

---

In Flutter, OverlayEntry allows developers to create overlay widgets on top of existing UI elements. In this article, we will dive into the process of creating OverlayEntry, tackling the challenge of resizing window, and demonstrate its potential with a QR popup on NetShare. Let’s get started!

### OverlayEntry overview

OverlayEntry renders widgets on top of existing UI elements, providing the ability to create interactive and visually appealing overlays. To create a simple OverlayEntry, we can use the following code snippet:

```dart
// Create an OverlayEntry
final overlayEntry = OverlayEntry(builder: (context) {}

// Add the OverlayEntry to the Overlay
Overlay.of(context, debugRequiredFor: widget).insert(overlayEntry!);
```

The problem is that we don’t have a specific location for it yet. So how to display OverlayEntry at the desired position?

Assume that we need to show the OverlayEntry at the button position when it is clicked. First, find the position of the button with `findRenderObject()` from button key and then use `localToGlobal()` to convert the local position to the global coordinate system. Next, use these positions in the Positioned widget. And finally fill up content for OverlayEntry, which is the child of Positioned.

```dart
 _showOverlay() {
    _clearOverlay();

    final RenderBox buttonRenderBox = _buttonKey.currentContext?.findRenderObject() as RenderBox;
    final buttonSize = buttonRenderBox.size;
    final buttonPosition = buttonRenderBox.localToGlobal(Offset.zero);

    overlayEntry = OverlayEntry(builder: (context) {
      return GestureDetector(
          behavior: HitTestBehavior.translucent,
          onTap: _clearOverlay,
          child: Stack(
            children: [
              Positioned(
                top: buttonPosition.dy + buttonSize.height,
                left: buttonPosition.dx + buttonSize.height,
                child: SizedBox(
                  width: overlaySize,
                  height: overlaySize,
                  child: Container(
                    alignment: Alignment.center,
                    color: Colors.blue,
                    child: const Text(
                      'Hello',
                      style: TextStyle(
                        color: Colors.white,
                        fontSize: 16.0,
                        decoration: TextDecoration.none,
                      ),
                    ),
                  ),
                ),
              )
            ],
          ));
    });
    
    // Add the OverlayEntry to the Overlay.
    Overlay.of(context, debugRequiredFor: widget).insert(overlayEntry!);
  }

  _clearOverlay() {
    overlayEntry?.remove();
    overlayEntry = null;
  }
```

You may notice the snippet code above call to `_clearOverlay()` method. It’s just to make sure that only one OverlayEntry is shown with the action that triggered it.

And this is the result:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710004536985/d52a3848-cb0d-4559-9b93-32e7a3fca973.gif align="center")

### Responsive OverlayEntry on Desktop UI

Even OverlayEntry can be displayed, it lacks the flexibility to adapt window resizing.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710004604930/dc0bd53f-4f39-474f-9492-6cccf4827db1.gif align="center")

Resizing a window should not hinder the seamless experience of our overlays. To ensure that our OverlayEntry adapts to changes in window size, we can leverage the power of LayoutBuilder. By wrapping our overlay content with a LayoutBuilder widget, we gain access to the constraints of the parent container.

```dart
  _showOverlay() {
    _clearOverlay();

    overlayEntry = OverlayEntry(builder: (context) {
      return GestureDetector(
        behavior: HitTestBehavior.translucent,
        onTap: _clearOverlay,
        child: LayoutBuilder(
          builder: (BuildContext context, BoxConstraints constraints) {
            final RenderBox buttonRenderBox = _buttonKey.currentContext?.findRenderObject() as RenderBox;
            final buttonSize = buttonRenderBox.size;
            final buttonPosition = buttonRenderBox.localToGlobal(Offset.zero);
            return Stack(
              children: [
                Positioned(
                  top: buttonPosition.dy + buttonSize.height,
                  left: buttonPosition.dx + buttonSize.height,
                  child: SizedBox(
                    width: overlaySize,
                    height: overlaySize,
                    child: Container(
                      alignment: Alignment.center,
                      color: Colors.blue,
                      child: const Text(
                        'Hello',
                        style: TextStyle(
                          color: Colors.white,
                          fontSize: 16.0,
                          decoration: TextDecoration.none,
                        ),
                      ),
                    ),
                  ),
                )
              ],
            );
          },
        ),
      );
    });

    // Add the OverlayEntry to the Overlay.
    Overlay.of(context, debugRequiredFor: widget).insert(overlayEntry!);
  }
```

Explain more about this, why do we include the button position calculation in LayoutBuilder? That’s because when the window resizes, the position of the button is also changed, so it is necessary to recalculate its position when the window resizing ends. Let’s see how this worked:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710004615388/26c4c9ba-bdb5-44ad-a6c2-7b8947a128ae.gif align="center")

That’s great, right? Let’s check the complete source code for this demo at this [gist](https://gist.github.com/huynguyennovem/55f36f71f5b17b9834d5ee4e7654fba5).

### Bringing it All Together — QR Popup on NetShare

To demonstrate the versatility of OverlayEntry, let’s take a look at QR popup on NetShare. [NetShare](https://github.com/huynguyennovem/netshare) is an open-source Flutter project that makes it easy to share data in a local network. The latest release includes support for Android, macOS, Windows, and Linux platforms.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710004647277/28a49538-26ba-4176-8164-b9b91dddcfd9.png align="center")

NetShare utilizes OverlayEntry to display a QR code overlay when the user clicks on the “QR” image button within the NetShare app in Server mode. The QR code overlay will provide a convenient way for users to quickly scan and access shared files on their mobile devices.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710004689104/ed4ab90e-2ae6-44f2-85ba-820c5962f9a4.gif align="center")

In this article, we explored the fundamentals of OverlayEntry, tackled the challenge of resizing window, and demonstrated its potential by creating a captivating QR popup on NetShare. Armed with this knowledge, you can now unleash your creativity and build visually stunning and interactive desktop applications with Flutter.

Happy Flutter-ing!

Stay in touch with me for updates:

* Twitter: [https://twitter.com/HuyNguyenTw](https://twitter.com/HuyNguyenTw)
    
* GitHub: [https://github.com/huynguyennovem](https://github.com/huynguyennovem)
    
* LinkedIn: [https://linkedin.com/in/huynguyennovem](https://linkedin.com/in/huynguyennovem)