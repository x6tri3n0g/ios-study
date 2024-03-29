# Objective-C Programming

> 📗 Ref. [Apple Developer Documentation](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011210-CH1-SW1)

<br />
<br />
<br />

# Objective-C 동작 원리

## TL;DR

1. 앱 실행 시 `main`함수는 `UIApplicationMain` 함수를 호출하며 `UIApplication`은 인스턴스를 생성하고 이를 `AppDelegate`에게 위임하여 관리한다.
2. 앱 로딩 과정에서 Info.plist 내에서 메인 nib 파일 이름을 명시했다면, 자동으로 해당 nib 파일을 읽어들여서 UI 관련 객체들을 초기화한다.

## iOS앱이 시작되는 과정을 살펴보자.

iOS앱은 사실 `UIApplication`이라는 클래스의 객체이다. 프로젝트의 `main` 함수는 기본적으로 `UIApplication` 클래스의 인스턴스를 만들어서 GUI를 사용하기 위한 런루프를 돌려주는 작업을 수행한다. 그리고 그 이후에 앱 내에서 일어나는 모든 처리는 `UIApplication` 객체가 관리하게 된다.

(1) `main` 함수가 실행된다.  
(2) `main` 함수는 다시 `UIApplicationMain` 함수를 호출한다.  
(3) 이 함수는 앱의 본체에 해당하는 객체인 `UIApplication` 객체를 생성한다.(인스턴스 생성)  
(4) 또 `Info.plist` 파일을 읽어들여 파일에 기록된 정보를 참고하여 그외에 필요한 데이터를 로드한다.  
(5) 메인 nib 파일을 사용하는 경우 (4)의 과정에서 로드된다.  
(6) 메인 nib 파일이 없거나, 그 속에 AppDelegate가 없는 경우, AppDelegate 객체를 만들고 앱 객체와 연결한다.  
(7) Run loop를 만드는 등 실행에 필요한 준비를 마무리해 간다.  
(8) 실행 완료를 앞두고 앱 객체가 AppDelegate에게 `application:didFinishLaunchingWithOptions:` 메시지를 보낸다.

## main.m?

`main.m`은 Objective-C로 구현된 iOS 어플리케이션의 진입 함수를 가진 파일이다. ObjC 기반의 iOS 프로젝트에는 main.m이 함수가 정의되어 있다. ObjC도 C계열 언어이며 그 구조가 동일하다. 따라서 프로그램이 실행될 때 가장 먼저 호출되어야하는 진입점이 main 함수가 되고 이는 프로그램 전체의 본체이다. Swift에서는 프로그램 실행 시 AppDelegate가 제일 처음 호출되는 반면 ObjC에서는 main.m을 호출gkrh AppDelegate를 호출한다. 그리고 대부분의 경우 AppDelegate의 `didFinishLaunchingWithOptions`에서 부터 시작하니 별로 건드릴 일은 없을 것 같다.(RN에서는 외부 라이브러리를 사용할 때 정도 수정했었다.)'

## UIApplicationMain 함수

iOS앱은 GUI 기반의 앱이다. 보통 간단하게 연습삼아 만드는 명령줄 프로그램과는 성격이 다르다. 프로그램은 실행된 이후에 (초기화와 관련된 몇 가지 자체 구성을 제외하면) 아무런 일도 하지 않으며, 사용자가 직접 끝내지 않는 이상 종료되지 않는다. 그러니까, `main()` 함수를 호출해서 `0`을 리턴하고 딱 끝나는 C 프로그램하고는 뭔가 다른 것이다.

`UIApplicationMain` 함수는 Cocoa Touch 프레임워크에서 앱의 라이프 사이클을 시작하는 함수이다. 이 함수는 `UIApplication` 객체의 인스턴스를 만들고, 해당 객체의 앱으로서 기능하기 위한 제반 조치를 취한다. Cocoa Touch에서 이 과정은 "앱 로딩 프로세스"라고 불린다. **앱 로딩 프로세스는 앱 객체가 생성되는 것을 시작으로, 앱 구동에 필요한 delegate, window, view, view controller들을 로딩하고 초기화하여 이들의 유기적인 관계를 재구성하며 GUI 동작에 필요한 런루프를 만드는 등의 많은 일을 포함한다.** 이 일 중의 거의 대부분은 프로그래머가 일일이 설정하거나 세팅해줄 필요가 없게 끔 되어 있다. 앱 로딩 과정에서 앱이 참조해야 하는 수 많은 정보들은 소스코드가 아니라 info-plist라는 프로퍼티 리스트 파일에 정의된다.

`UIApplicationMain` 함수는 `main`의 고정 파라미터인 `argc`, `argv`를 그대로 넘겨받으면서 추가적으로 두 가지 인자를 더 받고 있다. `principalClassName`은 앱 객체가 될 클래스의 이름인데 이건 특수한 경우가 아닌 이상 'UIApplication'일 것이다.(그래서 `nil`을 넣으면 `UIApplication`으로 대체한다.) 마지막 인자는 AppDelegate 클래스 이름이다. 역시 많은 경우에 nib 파일 내에 해당 객체가 이미 만들어져 있기 때문에, 이 값도 넘겨줄 필요가 없다.

하지만 nib 파일을 사용하지 않거나, nib 파일 내에 AppDelegate가 없는 경우도 있을 수 있다. 하지만 nib 파일은 없어도 되지만, iOS앱 구조상 AppDelegate는 절대 없어서 안된다. 물론, 그러니까 UIApplicationMain 함수에서 파라미터로까지 정의해놓지 않았겠나 싶다. nib 파일을 로딩했는데 AppDelegate가 없다면, `UIApplicationMain` 함수가 인자로 받은 이름을 기반으로, AppDelegate 역할을 수행할 객체를 자동으로 생성하고 AppDelegate로 임명하게 된다.

## AppDelegate의 역할

AppDelegate는 이름 그대로 앱 객체(Instance)의 대리인 역할을 한다. `UIApplication`은 서브 클래싱을 하는 경우도 드물고, 별로 그럴 이유도 없으며 그게 쉽지도 않다. 하지만 분명히 앱의 라이프 사이클의 여러 스테이지에서 수행되어야 하는 일은 있다. 따라서 앱 객체 클래스를 직접 서브 클래싱 하지 않고 delegate를 통해 처리하게 된다. 이러한 패턴처럼 Cocoz 및 Cocoa Touch는 상속을 통한 커스텀 클래스를 만들어 나가면서 객체를 확장하기 보다는 다른 이런 언어의 특성들을 사용하여 가능한 서브 클래싱을 피하는 쪽을 권장한다.

## 앱의 시작과 종료

앱이 시작되면 AppDelegate는 `application:didFinishLaunchingWithOptions:`라는 메시지를 받게 된다. **이 메소드는 앱이 실행 준비를 거의 마쳤을 때 호출되는데, 이 때 앱에 필요한 크리티컬한 데이터의 초기화를 하면 된다.** 단, 아직 앱이 화면에 나타나기 이전이므로 시간이 많이 걸리는 작업을 여기서 하면 안된다.(앱의 초기 로딩이 길어질 수 있다.) 데이터의 초기화를 위해 이 메소드는 거의 모든 경우에 오버라이드(Override)된다.

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
```

**특히 메인 nib 파일을 사용하지 않는 앱이라면, 윈도 및 메인 뷰가 없는 상태이므로, 이 메소드에서 생성하여 설치해야 한다.**

그리고 앱에 종료되기 직전에는 다시 이를 알려주는 메시지가 전달된다. AppDelegate에서는 잃어서는 안되는 사용자 데이터를 종료 전에 미리 저장해 둘 기회를 얻는 셈이다.

```objectivec
- (void)applicationWillTerminate:(UIApplication *)application
```

그 외에도 AppDelegate는 앱의 상태 변화에 대해 감지할 수 있다. 비활성화 된다거나 잠자기에 들어간다거나 저장된 상태로부터 복구된다거나 하는 것을 알아차리며, iOS 디바이스가 회전했을 때, 방향에 따라 화면을 회전할 것인지 결정할 수도 있다. AppDelegate의 주요 메소드에 대해서는 [`UIAppDelegateProtocol`](https://developer.apple.com/documentation/uikit/uiapplication#//apple_ref/doc/uid/TP40006728) 공식 문서를 참고하면된다.

<br />
<br />

# UIViewController

: 앱의 기초가 되는 내부 구조

-   앱은 ViewController로 이루어져 있다. 어떤 앱이던 한 개 이상의 ViewController를 가지고 있다.
-   ViewController는 View를 가지고 있다.
-   대부분의 앱은 수많은 ViewController로 이루어져있기도 하다.

## TL;DR

1. Updating the contents of the views, usually in response to changes to the underlying data.
2. Responding to user interactions with views.
3. Resizing views and managing the layout of the overall interface.

## View의 계층관리

> Each view controller manages a view hierarchy, the root view of which is stored in the view property of this class. The root view acts primarily as a container for the rest of the view hierarchy. The size and position of the root view is determined by the object that owns it, which is either a parent view controller or the app’s window. The view controller that is owned by the window is the app’s root view controller and its view is sized to fill the window.

-   각각의 ViewController는 View의 계층을 관리한다.
-   이 계층을 나눌 때에 가장 근원이 되는 것이 **RootView**이다.
    -   때문에 모든 ViewController는 한개의 RootView를 가진다.
-   화면에 표시되는 모든 뷰는 RootView의 계층 안에 있어야 한다.

## View와 관련된 Notifications을 다루는 것(Handling View-Related Notifications)

> When the visibility of its views changes, a view controller automatically calls its own methods so that subclasses can respond to the change. Use a method like viewWillAppear: to prepare your views to appear onscreen, and use the viewWillDisappear: to save changes or other state information. Use other methods to make appropriate changes.

<img width="582" alt="스크린샷 2021-03-05 17 33 13" src="https://user-images.githubusercontent.com/33711323/110089128-11816500-7dd9-11eb-9b2f-d5de79a09a76.png" align="center" />

-   `UIViewController` 객체는 UIView 객체들의 생성과 소멸, 이벤트 발생 및 행위들을 관리하는 곳이다.
-   때문에 View에 나타나기 직전, 나타나기 후, 사라질 때, 사라지기 직전일 때 시스템에서 단계별로 UIViewController에게 호출하고 필요한 메소드를 재정의하여 호출한다.

(1) **- (void)loadView**  
: UIViewController의 view가 생성될 때 호출

(2) **- (void)viewDidLoad**
: UIViewController가 인스턴스화된 후에 호출  
: 처음에 한 번 세팅해주어야하는 값들을 넣기에 적절하다.

(3) **- (void)viewWillAppear:(BooL)animated**  
: view가 화면에 보여지기 직전에 호출  
: animated 파라미터는 뷰가 애니메이션을 동반하여 보여지게 되는지 시스템에서 전달해주는 불리언 값이다.

(4) **- (void)viewWillLayoutSubviews**  
: view의 하위 뷰들의 레이아웃이 결정되기 직전에 호출  
: autoLayout을 하면, 이 때에 제약사항을 계산한다.

(5) **- (void)viewDidLayoutSubviews:(BooL)animated**  
: view가 화면에 보여진 직후에 호출  
: 화면이 표시된 이후 애니메이션 등을 보여주고 싶을 때 쓴다.

(6) **- (void)viewWillDisappear:(BOOL)animated**  
: view가 화면에서 사라지기 직전에 호출

(7) **-(void)viewDidDisappear:(BOOL)animated**  
: view가 화면에서 사라진 직후 호출

ViewController는 View가 아니기 때문에 화면에 보이지 않는다...!(🤭) 결국, View의 여러가지 요소들을 관장하고 있는 '무언가'인 것이다.

ViewController는 하나의 View를 꼭 가지고 있을 수 있지만, View는 꼭 하나의 View Controller에 속해있을 필요는 없다.(View가 너무 많아질 때에는 메모리가 모자라므로 필요 없을 때는 없애고, 다시 찾아올 수도 있게 한 것)

## View에서 일어나는 user interactions를 받는다

-   **사용자의 모든 이벤트는 ViewController가 받는다.**
-   그 후에 **각 View에 따라 관련 action 메소드나 delegate를 통해 처리한다.**

## ViewController는 데이터 중계자 역할을 한다.

<img width="794" alt="스크린샷 2021-03-05 17 55 42" src="https://user-images.githubusercontent.com/33711323/110091710-17c51080-7ddc-11eb-8d45-373720e84384.png" align="center" />

-   ViewController는 자신이 관리하는 View와 내부의 데이터를 중계하는 역할을 한다.

## Resource 관리

-   모든 View들을 위에 올리고 있는 ViewController는 모든 View의 책임과 관리를 맡고 있다.
-   **didReceiveMemoryWarning method**
    -   앱 사용 중에 메모리가 부족할 때 불리는 메소드
    -   오랫동안 사용하지 않은 객체와 다시 쉽게 만들 수 있는 객체를 제거한다.
    -   이후에 다시 불리울 때에는 View를 다시 그린다.
        : 🏷 참고) 메모리 워닝이 일어날 때 가장 먼저 지워지는 부분은 View이다. 때문에 리소스 관리를 통해 계속 View가 제거/생성이 반복되는 것이다.(이를 통해 사실, ViewDidLoad)가 한 번만 불리는 것이 아닌 것을 알 수 있다.) 때문에 우리는 View의 인스턴스를 생성할 때, 꼭 객체가 nil인지 확인하고 해야한다.
