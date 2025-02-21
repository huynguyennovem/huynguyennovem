---
title: "Streamlining exiting your Flutter desktop app with Shortcut keys"
seoTitle: "Streamlining exiting your Flutter desktop app with Shortcut keys"
datePublished: Sat Mar 09 2024 18:07:04 GMT+0000 (Coordinated Universal Time)
cuid: cltkedzdm00010aiafg5tbkkk
slug: streamlining-exiting-your-flutter-desktop-app-with-shortcut-keys
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1724420274257/7573bade-7b56-4fce-ac2f-d95130d52e92.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1724420291521/6606a751-f139-43fe-bca3-2a1a328260a3.jpeg
tags: tutorial, flutter, shortcuts, desktop-apps

---

Flutter has emerged as a versatile framework for developing desktop applications, offering extensive support for handling keyboard events and providing a seamless user experience.

In this article, we'll explore how Flutter simplifies the process of exiting desktop apps using shortcut keys. We'll delve into the solution for handling app, tackle the issue when working with the Navigator, embrace user-friendly API, and finally, demonstrate of integrating these solutions into the open-source Flutter project NetShare.

### Capture keyboard events

Managing app exit through shortcut keys in a Flutter desktop app lies in utilizing the `RawKeyboard.instance.addListener` to monitor raw keyboard events. By incorporating this approach within your `initState()` method, you can effectively capture user keyboard interactions. Here's how you can set it up:

```dart
@override
void initState() {
  super.initState();

  RawKeyboard.instance.addListener((key) {
    // Handle the raw keyboard event here.
  });
}
```

Within the keyboard callback, you gain the ability to examine the `key` parameter, which reveals which key was either pressed or released.

Moreover, for precise shortcut key detection, you can leverage the `key.isMetaPressed` and `key.isControlPressed` properties in conjunction with the `key.logicalKey` property.

This allows you to identify specific key combinations like `Command + W` (on macOS) or `Control + W` (on Windows/Linux) to initiate the app's exit process:

```dart
RawKeyboard.instance.addListener((key) {
  if (value.isMetaPressed && value.logicalKey == LogicalKeyboardKey.keyW ||
        value.isControlPressed && value.logicalKey == LogicalKeyboardKey.keyW) {
    SystemNavigator.pop();
  }
});
```

Rather than listening to keyboard events within the `initState()`, an alternative approach is to register listener within MaterialApp's builder, as below:

```dart
MaterialApp.router(
  routerConfig: _router,
  builder: (context, child) {
    // Handle keyboard listener here
    RawKeyboard.instance.addListener((RawKeyEvent value) => _handleKeyEvent(context, value));
    return child ?? const SizedBox.shrink();
  },
)
```

Now, give the app a test run to observe it functioning as expected.

However, should the application exit abruptly? Typically, we expect desktop applications to prompt users for confirmation before closing. Although this behavior can often be configured in the application's settings, let's explore such cases in the next section.

### Navigating the confirm dialog

Suppose we need to request user confirmation before exiting the app by invoking `SystemNavigator.pop()`. Let's implement a Dialog for this purpose:

```dart
void _handleKeyEvent(BuildContext context, RawKeyEvent value) {
  // If user pressed Command/Control + W keys, quit the app
  if (value.isMetaPressed && value.logicalKey == LogicalKeyboardKey.keyW ||
      value.isControlPressed && value.logicalKey == LogicalKeyboardKey.keyW) {
    _showQuitConfirmationDialog(context);
  }
}

void _showQuitConfirmationDialog(BuildContext context) {
  showDialog(
    context: context,
    builder: (context) {
      return AlertDialog(
        title: const Text('Quit App'),
        content: const Text('Are you sure you want to quit the app?'),
        actions: [
          TextButton(
            onPressed: () {
              Navigator.of(context).pop(); // Close the dialog
            },
            child: const Text('Cancel'),
          ),
          TextButton(
            onPressed: () {
              // Close the app
              SystemNavigator.pop();
            },
            child: const Text('Quit'),
          ),
        ],
      );
    },
  );
}
```

However, you might encounter an issue with the Navigator, as indicated by the error message:

```console
══╡ EXCEPTION CAUGHT BY SERVICES LIBRARY ╞══════════════════════════════════════════════════════════
The following assertion was thrown while processing a raw key listener:
Navigator operation requested with a context that does not include a Navigator.
The context used to push or pop routes from the Navigator must be that of a widget that is a
descendant of a Navigator widget.

When the exception was thrown, this was the stack:
#0      Navigator.of.<anonymous closure> (package:flutter/src/widgets/navigator.dart:2695:9)
#1      Navigator.of (package:flutter/src/widgets/navigator.dart:2702:6)
#2      showDialog (package:flutter/src/material/dialog.dart:1419:19)
#3      MyApp.showQuitAppConfirmationDialog (package:netshare/main.dart:131:5)
#4      MyApp._handleKeyEvent (package:netshare/main.dart:122:7)
#5      MyApp.build.<anonymous closure>.<anonymous closure> (package:netshare/main.dart:111:67)
#6      RawKeyboard.handleRawKeyEvent (package:flutter/src/services/raw_keyboard.dart:705:19)
#7      KeyEventManager.handleRawKeyMessage (package:flutter/src/services/hardware_keyboard.dart:1050:30)
#8      BasicMessageChannel.setMessageHandler.<anonymous closure> (package:flutter/src/services/platform_channel.dart:216:49)
#9      _DefaultBinaryMessenger.setMessageHandler.<anonymous closure> (package:flutter/src/services/binding.dart:567:35)
#10     _invoke2 (dart:ui/hooks.dart:345:13)
#11     _ChannelCallbackRecord.invoke (dart:ui/channel_buffers.dart:45:5)
#12     _Channel.push (dart:ui/channel_buffers.dart:135:31)
#13     ChannelBuffers.push (dart:ui/channel_buffers.dart:343:17)
#14     PlatformDispatcher._dispatchPlatformMessage (dart:ui/platform_dispatcher.dart:689:22)
#15     _dispatchPlatformMessage (dart:ui/hooks.dart:257:31)

Event: RawKeyDownEvent#f8a0b(logicalKey: LogicalKeyboardKey#510a7(keyId: "0x00000077", keyLabel:
  "W", debugName: "Key W"), physicalKey: PhysicalKeyboardKey#b9a3b(usbHidUsage: "0x0007001a",
  debugName: "Key W"), repeat: false)
════════════════════════════════════════════════════════════════════════════════════════════════════
```

The error message indicates that you are trying to use the Navigator to push or pop routes from a context that does not include a Navigator. This make sense as we are using Navigator outside of MaterialApp.router widgets. The solution to this problem involves using a GlobalKey. First, declare a GlobalKey:

```dart
final GlobalKey<NavigatorState> _navigatorKey = GlobalKey<NavigatorState>();
```

Then, add it to Material's `navigatorKey`:

```dart
MaterialApp(
  navigatorKey: _navigatorKey
)
```

If you use `go_router` package as route management, you can add it to GoRoute constructor.

```dart
final GoRouter _router = GoRouter(
  navigatorKey: _navigatorKey
)

MaterialApp.router(
  routerConfig: _router
)
```

With the GlobalKey properly set, you can now use the context from `_navigatorKey` to address the issue. And now we can remove `BuildContext context` parameter from `_handleKeyEvent` method:

```dart
void _handleKeyEvent(BuildContext context, RawKeyEvent value) {
  // If user pressed Command/Control + W keys, quit the app
  if (value.isMetaPressed && value.logicalKey == LogicalKeyboardKey.keyW ||
      value.isControlPressed && value.logicalKey == LogicalKeyboardKey.keyW) {
    _showQuitConfirmationDialog(_navigatorKey.currentContext!);
  }
}
```

Voila! The app can gracefully exit after the user confirms, ensuring a smoother user experience.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710007442550/1a7bf60f-31c7-4f05-a2a8-3c4638d39388.gif align="left")

Hold on a moment, there is one more issue to address. While the confirmation dialog is displayed, if the user presses Command/Control + W again, another dialog will overlap the current one.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710007453379/9da096cf-9ebf-413b-9975-a93226086484.gif align="left")

To prevent this from happening and ensure a seamless user experience, we need to implement a mechanism that handles such scenarios gracefully. The most straightforward solution involves the use of a flag. To resolve this issue, declare a new flag and update the code as follows:

```dart
bool _isKeyboardListenerEnabled = true;

void _handleKeyEvent(RawKeyEvent value) async {
  if (!_isKeyboardListenerEnabled) return;

  // If user pressed Command/Control + W keys, quit the app
  if (value.isMetaPressed && value.logicalKey == LogicalKeyboardKey.keyW || value.isControlPressed && value.logicalKey == LogicalKeyboardKey.keyW) {
    if (_navigatorKey.currentContext == null) return;

    // show confirm dialog
    _showQuitAppConfirmationDialog(_navigatorKey.currentContext!, (confirmCallback) {
      if (confirmCallback) {
        SystemNavigator.pop(); // Quit the app
      }
      // listen keyboard again
      _isKeyboardListenerEnabled = true;
    });
  }
}


void _showQuitAppConfirmationDialog(BuildContext context, Function(bool)? confirmCallback) {
  // Disable the keyboard listener.
  _isKeyboardListenerEnabled = false;

  showDialog(
    barrierDismissible: false,
    context: context,
    builder: (context) {
      return AlertDialog(
        title: const Text('Quit App'),
        content: const Text('Are you sure you want to quit the app?'),
        actions: [
          TextButton(
            onPressed: () {
              confirmCallback?.call(false);
              Navigator.of(context).pop(); // Close the dialog
            },
            child: const Text('Cancel'),
          ),
          TextButton(
            onPressed: () {
              // Close the app
              confirmCallback?.call(true);
            },
            child: const Text('Quit'),
          ),
        ],
      );
    },
  );
}
```

Now, run the app and repeatedly press the `Command/Control + W` keys. You'll notice that only one confirmation dialog appears, effectively resolving the issue.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710007488227/c2b2e595-b37d-44c6-aca6-bb4df20bff6f.gif align="left")

### Embracing user-friendly API

Per [Actions API revision](https://docs.flutter.dev/release/breaking-changes/actions-api-revision):

> In Flutter an Intent is an object that’s typically bound to a keyboard key combination using the Shortcuts widget. An Intent can be bound to an Action, which can update the application’s state or perform other operations.

This approach simplifies the process of capturing shortcut keys by utilizing Intent/Action pairs from the Flutter API. Thanks to Reddit folk [eibaan](https://www.reddit.com/user/eibaan/) for sharing this approach. I received his [comment](https://www.reddit.com/r/FlutterDev/comments/17354xo/comment/k41qyup/?utm_source=share&utm_medium=web2x&context=3) after sharing my article on the FlutterDev Reddit channel.

Now, you can streamline your code by removing the keyboard listener from the previous solution and implementing the following steps:

Firstly, declare an Intent named `QuitIntent`:

```dart
class QuitIntent extends Intent {
  const QuitIntent();
}
```

Next, define shortcuts and Actions:

```dart
MaterialApp.router(
  routerConfig: _router,
  shortcuts: const {
    SingleActivator(LogicalKeyboardKey.keyW, meta: true): QuitIntent(),
    SingleActivator(LogicalKeyboardKey.keyW, control: true): QuitIntent(),
  },
  builder: (context, child) {
    return Actions(actions: {
      QuitIntent: CallbackAction(onInvoke: (intent) => _handleIntent()),
    }, child: child ?? const SizedBox.shrink());
  },
)
```

Finally, update `_handleKeyEvent()` method to `_handleIntent()`. You no longer need to check for pressed keys as this approach handles Intents seamlessly:

```dart
void _handleIntent() {
  if (!_isKeyboardListenerEnabled) return;
  if (_navigatorKey.currentContext == null) return;

  // show confirm dialog
  _showQuitAppConfirmationDialog(_navigatorKey.currentContext!, (confirmCallback) {
    if (confirmCallback) {
      SystemNavigator.pop(); // Quit the app
    }
    // listen keyboard again
    _isKeyboardListenerEnabled = true;
  });
}
```

Run the app to observe that it behaves similarly to the first solution using keyboard events, but now with the enhanced user-friendly API.

Check out the complete sample code at [flutter-desktop-quit-app](https://github.com/huynguyennovem/flutter-desktop-quit-app).

### Bringing it all together — Integration with NetShare

NetShare is an open-source Flutter project that makes it easy to share data in a local network. The latest release includes support for Android, macOS, Windows, and Linux platforms.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710007566673/60feb82b-2328-490d-8019-ccc9961338ce.png align="left")

The integration of quit app shortcut keys into NetShare enhances the user experience by allowing users to swiftly exit the application, aligning with the natural behavior found in other native desktop applications.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1710007585629/5a5cb99b-7e4b-4184-a986-59a6058d6b50.gif align="left")

### Conclusion

Managing the exit process of a Flutter desktop app with shortcut keys is essential for providing a user-friendly experience. By incorporating these techniques, your Flutter desktop app can achieve a level of professionalism and user-friendliness that mirrors that of established desktop applications, contributing to an enhanced user journey.

Stay in touch with me for updates:

* Twitter: [https://twitter.com/HuyNguyenTw](https://twitter.com/HuyNguyenTw)
    
* GitHub: [https://github.com/huynguyennovem](https://github.com/huynguyennovem)
    
* LinkedIn: [https://linkedin.com/in/huynguyennovem](https://linkedin.com/in/huynguyennovem)