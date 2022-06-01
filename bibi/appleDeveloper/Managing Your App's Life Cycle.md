

# Managing Your App's Life Cycle

> https://developer.apple.com/documentation/uikit/app_and_environment/managing_your_app_s_life_cycle

---

---



---

## 앱의 라이프사이클 관리

**앱이 foreground나 background에 있을 때 시스템 알림에 응답하고, 다른 중요한 시스템 관련 이벤트를 처리합니다.**

### 개요

앱의 현재 상태는 언제든지 무엇을 할 수 있고 할 수 없는지를 결정합니다. 예를 들어, foreground의 앱은 사용자의 주의를 갖고 있습니다. 따라서 CPU를 포함한 시스템 자원에 대해 우선순위를 집니다. 대조적으로, background의 앱은 화면 밖에 있기 때문에, 가능한 적은 일을 해야 하며 아무것도 하지 않으면 더 좋습니다. 앱이 상태에서 상태를 전환할 때, 당신은 그에 따라서 그 동작을 조정해야만 합니다. 

앱의 상태가 바뀔 때, UIKit은 절적한 델리게이트 객체의 메서드를 호출함으로써 당신에게 알려줍니다:

- iOS 13 이후에서는, scene 기반 앱에서 라이프사이클 이벤트에 응답하기 위해 UISceneDelegate 객체를 사용하십시오.
- iOS 12 이전 버전에서는, 라이프사이클 이벤트에 응답하기 위해 UIApplicationDelegate를 사용하십시오.

> 노트
>
> 만약 당신의 앱에서 scene support를 사용할 수 있다면, iOS는 13 이후 버전에서 항상 scene delegate를 사용합니다. 12 이전 버전에서 시스템은 app delegate를 사용합니다.

## Scene 기반의 라이프사이클 이벤트에 응답하기

당신의 앱이 scene을 지원한다면, UIKit은 각 snene에 개별적인 라이프사이클 이벤트를 전달합니다.  scene은 기기에서 실행되는 앱의 UI의 하나의 인스턴스를 나타냅니다. 사용자는 각각의 앱에서 여러 scene을 만들 수 있고, 그것들을 개별적으로 보이게 하거나 숨길 수 있습니다. 각각의 scene은 자신만의 라이프사이클을 가지고 있기 때문에, 각자 다른 실행 상태가 될 수 있습니다. 예를 들어, 다른 scene들이 background에 있거나 중지된 동안, 어떤 scene은 foreground에 있을 수 있습니다.

> 중요
>
> scene은 옵트인(opt-in) 특성을 지원합니다. 기본 지원을 가능하게 하기 위해서는, UIApplicationSceneManifest 키를 앱의 Info.plist 파일에 추가하십시오. [Specifying the Scenes Your App Supports](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/specifying_the_scenes_your_app_supports)에 자세히 나와 있습니다.

다음 그림은 scene의 상태 전환을 나타냅니다. 사용자나 시스템이 당신의 앱의 새로운 scene을 요청할 때, UIKit은 scene을 만들고 그것을 연결되지 않은 상태(`unattached`)로 둡니다. 사용자가 요청한 scene은 foreground로 빠르게 이동해 화면에 나타납니다. 시스템이 요청한 scene은 일반적으로 background로 이동해 이벤트를 처리할 수 있게 합니다. 예를 들어, 시스템은 위치 이벤트를 처리하기 위해 scene을 백그라운드(`background`)에서 실행할 수 있습니다. 사용자가 당신의 앱의 UI를 없앨 때, UIKit은 관련된 scene을 백그라운드 상태로, 최종적으로는 정지(`suspended`) 상태로 전환합니다. UIKit은 리소스를 되찾기 위해, 언제든지 `background` 또는 `suspended` 상태인 scene의 연결을 끊고 `unattached`상태로 되돌릴 수 있습니다.

![An illustration showing the state transitions for a scene-based app. Scenes start in the unattached state and move to the foreground-active or background state. The foreground-inactive state acts as a transition state.](https://docs-assets.developer.apple.com/published/c834d5ac04/scene-state@2x.png)

다음 작업들을 수행하기 위해 scene 전환을 사용하십시오:

- UIKit이 당신의 앱에 scene을 연결할 때, 당신의 scene의 첫 UI를 구성하고 scene에 필요한 데이터를 로드합니다.
- `foreground-active`상태로 전환할 때, UI를 구성하고 사용자와의 상호작용을 준비하십시오.  [Preparing Your UI to Run in the Foreground](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_foreground)을 참조하십시오.
- `foreground-active`상태에서 벗어날 때, 데이터를 저장하고 앱의 동작을 조용하게 하십시오. [Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background)를 참조하십시오.
- `background`상태에 들어갈 때, 중요한 작업들을 끝내고, 가능한 많은 메모리를 해제하고, 앱의 스냅샷을 준비하십시오. [Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background)를 참조하십시오.
- scene의 연결이 해제될 때(disconnection), scene과 관련된 모든 공유 자원들을 정리하십시오.
- scene 관련 이벤트에 더해, UIApplicationDelegate객체를 사용해 앱의 시작에 반드시 응답해야 합니다. 앱 시작 시점에 무엇을 해야하는지에 대한 정보를 위해 [Responding to the Launch of Your App](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app)를 참조하십시오.

## App 기반의 라이프사이클 이벤트에 응답하기

iOS 12 이하 버전이나, scene을 지원하지 않는 앱에서는, UIKit은 모든 라이프사이클 이벤트를 UIApplicationDelegate 객체에 전달합니다. 앱의 델리게이트는 앱의 개별적인 화면에 보이는 것을 포함해 모든 window들을 관리합니다. 그 결과, 앱의 상태 전환이 외부 디스플레이의 컨텐츠를 포함한 앱의 전체 UI에 영향을 끼칩니다. 

아래 그림은 앱 델리게이트 객체와 관련된 상태 전환을 보여줍니다. 앱 시작 이후, 시스템은 UI가 곧 화면에 나타날지 아닌지에 따라 앱을 `inactive` 또는 `background`상태에 둡니다. foreground로 시작할 때, 시스템은 앱을 `active`상태로 자동으로 전환합니다. 이후, 앱의 상태는 앱이 종료될 때 까지`active` 와 `background` 사이에서 유동적으로 변합니다.

![An illustration showing the state transitions for an app without scenes. The app launches into the active or background state. An app transitions through the inactive state. ](https://docs-assets.developer.apple.com/published/64a2e0dab8/app-state@2x.png)

아래의 작업을 수행하기 위해 앱 전환을 사용하십시오:

- 시작할 때, 앱의 데이터 구조와 UI를 초기화하십시오. [Responding to the Launch of Your App](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app)를 참조하십시오.
- 활성화 상태(activation)일 때, UI 구성을 끝내고 유저와의 상호작용을 준비하십시오. [Preparing Your UI to Run in the Foreground](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_foreground)를 참조하십시오.
- 비활성화 상태(deactivation) 중에, 데이터를 저장하고 앱의 활동을 조용하게 하십시오. [Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background)를 참조하십시오.
- `background` 상태에 진입할 때, 중요한 작업을 완료하고, 가능한 많은 메모리를 해제하고, 앱의 스냅샷을 준비하십시오. [Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background)를 참조하십시오.
- 종료할 때, 모든 작업을 즉시 중단하고 모든 공유 리소스를 해제하십시오. [`applicationWillTerminate(_:)`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623111-applicationwillterminate)을 참조하십시오.



---

## (원문) Managing Your App's Life Cycle

Respond to system notifications when your app is in the foreground or backgroun를 차, and handle other significant system-related events.

## Overview

The current state of your app determines what it can and cannot do at any time. For example, a foreground app has the user’s attention, so it has priority over system resources, including the CPU. By contrast, a background app must do as little work as possible, and preferably nothing, because it is offscreen. As your app changes from state to state, you must adjust its behavior accordingly.

When your app’s state changes, UIKit notifies you by calling methods of the appropriate delegate object:

- In iOS 13 and later, use [`UISceneDelegate`](https://developer.apple.com/documentation/uikit/uiscenedelegate) objects to respond to life-cycle events in a scene-based app.
- In iOS 12 and earlier, use the [`UIApplicationDelegate`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate) object to respond to life-cycle events.

>  Note
>
>  If you enable scene support in your app, iOS always uses your scene delegates in iOS 13 and later. In iOS 12 and earlier, the system uses your app delegate.

## Respond to Scene-Based Life-Cycle Events

If your app supports scenes, UIKit delivers separate life-cycle events for each. A scene represents one instance of your app’s UI running on a device. The user can create multiple scenes for each app, and show and hide them separately. Because each scene has its own life cycle, each can be in a different state of execution. For example, one scene might be in the foreground while others are in the background or are suspended.

>  Important
>
> Scene support is an opt-in feature. To enable basic support, add the [`UIApplicationSceneManifest`](https://developer.apple.com/documentation/bundleresources/information_property_list/uiapplicationscenemanifest) key to your app’s `Info.plist` file as described in [Specifying the Scenes Your App Supports](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/specifying_the_scenes_your_app_supports).

The following figure shows the state transitions for scenes. When the user or system requests a new scene for your app, UIKit creates it and puts it in the unattached state. User-requested scenes move quickly to the foreground, where they appear onscreen. A system-requested scene typically moves to the background so that it can process an event. For example, the system might launch the scene in the background to process a location event. When the user dismisses your app's UI, UIKit moves the associated scene to the background state and eventually to the suspended state. UIKit can disconnect a background or suspended scene at any time to reclaim its resources, returning that scene to the unattached state.



![An illustration showing the state transitions for a scene-based app. Scenes start in the unattached state and move to the foreground-active or background state. The foreground-inactive state acts as a transition state.](https://docs-assets.developer.apple.com/published/c834d5ac04/scene-state@2x.png)



Use scene transitions to perform the following tasks:

- When UIKit connects a scene to your app, configure your scene’s initial UI and load the data your scene needs.
- When transitioning to the foreground-active state, configure your UI and prepare to interact with the user. See [Preparing Your UI to Run in the Foreground](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_foreground).
- Upon leaving the foreground-active state, save data and quiet your app’s behavior. See [Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background).
- Upon entering the background state, finish crucial tasks, free up as much memory as possible, and prepare for your app snapshot. See [Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background).
- At scene disconnection, clean up any shared resources associated with the scene.
- In addition to scene-related events, you must also respond to the launch of your app using your [`UIApplicationDelegate`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate) object. For information about what to do at app launch, see [Responding to the Launch of Your App](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app).

## Respond to App-Based Life-Cycle Events

In iOS 12 and earlier, and in apps that don't support scenes, UIKit delivers all life-cycle events to the [`UIApplicationDelegate`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate) object. The app delegate manages all of your app’s windows, including those displayed on separate screens. As a result, app state transitions affect your app's entire UI, including content on external displays.

The following figure shows the state transitions involving the app delegate object. After launch, the system puts the app in the inactive or background state, depending on whether the UI is about to appear onscreen. When launching to the foreground, the system transitions the app to the active state automatically. After that, the state fluctuates between active and background until the app terminates.



![An illustration showing the state transitions for an app without scenes. The app launches into the active or background state. An app transitions through the inactive state. ](https://docs-assets.developer.apple.com/published/64a2e0dab8/app-state@2x.png)



Use app transitions to perform the following tasks:

- At launch, initialize your app’s data structures and UI. See [Responding to the Launch of Your App](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app).
- At activation, finish configuring your UI and prepare to interact with the user. See [Preparing Your UI to Run in the Foreground](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_foreground).
- Upon deactivation, save data and quiet your app’s behavior. See [Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background).
- Upon entering the background state, finish crucial tasks, free up as much memory as possible, and prepare for your app snapshot. See [Preparing Your UI to Run in the Background](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background).
- At termination, stop all work immediately and release any shared resources. See [`applicationWillTerminate(_:)`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623111-applicationwillterminate).

---

---

## 내용 정리

### AppDelegate 

뷰의 라이프사이클처럼, 앱의 라이프사이클 (background, foreground, 등등등)마다의 상황을 관리

- UIKit이 appdelegate 내의 메서드를 호출해 준다. 우리는 메서드 구현만 해주면 된다! 뷰가 다 그려지고 보이기 직전에 viewdidload()가 호출되는 것처럼!
- 예를 들어 로그인한 유저와 로그인하지 않은 유저에게 다른 화면을 보여주려면 appdelegate 내에서 처리할 수 있다.

### SceneDelegate

 : 아이패드 등에서 앱을 여러 개 동시에 띄우는 상황에 사용

- 여러 scene이 생성되는 상황에서 핸들링 담당
- window : 뷰의 최상위 계층이다.

스냅샷 snapshot : 현재 앱 화면의 이미지.



-  Foreground Inactive : 화면에 보이지만 어떤 동작도 할 수 없는 상태. 예를 들어 런치스크린
- Foreground Active : 화면에 보이고 실행되는 상태.



---

## 단어 정리

- foreground 전경
- handle 처리하다
- overview 개요
- at any time 언제든지
- offscreen 화면 밖의
- preferably 가능한 ...하면 더 좋은
  - preferable 더 좋은. 선호되는.
- adjust 조정하다
- accordingly 그에 따라서
- suspend 정지하다
- opt-in : 어떤 활동, 합의의 일부가 되는 것을 선택하다. 자신이 선택해서 참가함
  - 반대말은 opt-out. 일부가 되지 않는 것을 선택하다.
- figure 그림
- transition 전환
- onscreen 화면에
- process 처리하다
- reclaim 반환을 요구하다. 다시 요구하다. 되찾다.
- upon = on
  - upon leaving = on leaving = 벗어날 때
- disconnection 연결 해제
  - unattached 연결되지 않은
- free up ...를 해방하다
- involve 관련시키다
- be about to 막 ...하려고 하다
- fluctuate 변동을 거듭하다.





