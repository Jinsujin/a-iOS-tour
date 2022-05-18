

# Managing Your App's Life Cycle

> https://developer.apple.com/documentation/uikit/app_and_environment/managing_your_app_s_life_cycle

---

---

# Managing Your App's Life Cycle

Respond to system notifications when your app is in the foreground or background, and handle other significant system-related events.

## Overview

The current state of your app determines what it can and cannot do at any time. For example, a foreground app has the user’s attention, so it has priority over system resources, including the CPU. By contrast, a background app must do as little work as possible, and preferably nothing, because it is offscreen. As your app changes from state to state, you must adjust its behavior accordingly.

When your app’s state changes, UIKit notifies you by calling methods of the appropriate delegate object:

- In iOS 13 and later, use [`UISceneDelegate`](https://developer.apple.com/documentation/uikit/uiscenedelegate) objects to respond to life-cycle events in a scene-based app.
- In iOS 12 and earlier, use the [`UIApplicationDelegate`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate) object to respond to life-cycle events.

Note

If you enable scene support in your app, iOS always uses your scene delegates in iOS 13 and later. In iOS 12 and earlier, the system uses your app delegate.