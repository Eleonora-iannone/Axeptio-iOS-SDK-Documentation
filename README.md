<h1>
  <img src="https://axeptio.imgix.net/2024/07/e444a7b2-ea3d-4471-a91c-6be23e0c3cbb.png" alt="Descrizione immagine" width="80" style="vertical-align: middle; margin-right: 10px;" />
  Axeptio iOS SDK Documentation
</h1>

Welcome to the **Axeptio iOS SDK Samples project!** This repository provides a comprehensive guide on how to integrate the **Axeptio iOS SDK** into your mobile applications. It showcases two distinct modules: one for **Swift** using Swift Package Manager and one for **Objective-C** using CocoaPods. Below you'll find detailed instructions and code examples to help you integrate and configure the SDK within your iOS app.

# Table of Contents 
1. [Requirements](#requirements)
2. [Clone the repository](#clone-the-repository)
3. [Adding the SDK](#adding-the-sdk)
   - [Using CocoaPods](#using-cocoapods)
   - [Using Swift Package Manager](#using-swift-package-manager)
4. [Initializing the SDK](#adding-the-sdk)
   - [Swift](#swift)
   - [Objective C](#objective-c)
5. [Set up the SDK UI](#set-up-the-sdk-ui)
   - [Swift](#swift)
   - [Objective C](#objective-c)
     - [Issues with the Consent Popup (Objective-C)](#issues-with-the-consent-popup-objective-c)
   - [SwiftUI Integration](#swiftui-integration)

   
## Requirements
The Axeptio iOS SDK is distributed as a pre-compiled binary package, delivered as an `XCFramework`. It supports iOS versions >= 15.

Before starting, make sure you have:

- Xcode >= 15
- CocoaPods or Swift Package Manager for dependency management.

**Note:** Please be aware that it is not possible to test a custom banner without an active and valid Axeptio plan. A valid Axeptio plan is required to configure and preview custom consent banners during development.

Ensure the **following keys** are added to your `Info.plist` file to comply with app tracking and security policies:
```xml
<key>NSUserTrackingUsageDescription</key>
<string>Your data will be used to deliver personalized ads to you.</string>
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```
## Clone the Repository
To get started, clone the repository to your local machine:

```bash
git clone https://github.com/axeptio/sample-app-ios
```
## Adding the SDK
The package can be added to your project using either **CocoaPods** or **Swift Package Manager**. Both dependency managers for iOS and are supported by the Axeptio SDK.

### Using CocoaPods
If your project uses CocoaPods, you can easily add the Axeptio SDK by following these steps:
#### Prerequisites
- Xcode version 15 or later
- CocoaPods version compatible with XCFrameworks (latest version recommended), if you haven' already, install the latest version of [CocoaPods](https://guides.cocoapods.org/using/getting-started.html)
### Steps
- Open your `Podfile` in the root directory of your project
```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '15.0'
use_frameworks!

target 'MyApp' do
  pod 'AxeptioIOSSDK'
end
```
- run the following command to install the dependency:
```bash
pod install
```

### Using Swift Package Manager
To integrate the Axeptio iOS SDK into your Xcode project using Swift Package Manager, follow these steps:
#### Steps
- Open your Xcode project.
- In the **Project Navigator**, select your project
- Under the **PROJECT** section, navigate to the Package Dependencies tab
- Click the **+** button to add a new package dependency
- In the search bar, paste the following package URL: `https://github.com/axeptio/axeptio-ios-sdk`
- Select the **AxeptioIOSSDK** package from the list of available packages
- Click Add Package.
- In the **Choose Package Products screen**, confirm the selection and click **Add Package** to complete the integration

## Initializing the SDK
To initialize the Axeptio SDK in your iOS project, import the `AxeptioSDK` module into your `AppDelegate` and initialize the SDK with the appropriate configuration. 

### Swift
```swift
import UIKit
import AxeptioSDK

class ViewController: UIViewController, UITableViewDataSource {

    @IBOutlet weak var tableView: UITableView!

    override func viewDidLoad() {
        super.viewDidLoad()

        // Registra l'identificatore della cella per UserDefaultsCell
        tableView.register(UITableViewCell.self, forCellReuseIdentifier: "UserDefaultsCell")
        
        // Chiamata di setupUI per mostrare il popup di consenso quando appropriato
        Axeptio.shared.setupUI()
    }

    // UITableViewDataSource methods
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 1
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "UserDefaultsCell", for: indexPath)
        cell.textLabel?.text = "UserDefaults Button"
        return cell
    }
}
```
### Objective C
```objc
#import "AppDelegate.h"

@import AxeptioSDK;

@interface AppDelegate ()

@end

@implementation AppDelegate


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    AxeptioService targetService = AxeptioServiceBrands; // or AxeptioServicePublisherTcf
    // sample init
    [Axeptio.shared initializeWithTargetService:targetServiceclientId:@"<Your Client ID>" cookiesVersion:@"<Your Cookies Version>"];

    // or with a token set from an other device
    [Axeptio.shared initializeWithTargetService:targetServiceclientId:@"<Your Client ID>" cookiesVersion:@"<Your Cookies Version>" token:@"<Token>"];

    return YES;
}
```
## Set up the SDK UI
> **[!IMPORTANT]** The `setupUI` method should be invoked **only** from your main/entry `UIViewController`, typically once during the application launch. By calling this method, the consent notice and preference views will be displayed **only if necessary** and **once the SDK is fully initialized**.

In order to display the consent and preference views and interact with the user, ensure that the `setupUI` method is called from your main `UIViewController`. The consent popup and preferences management will be shown based on the SDK initialization and the user's consent requirements.

### Swift
```swift
import UIKit

import AxeptioSDK
​
class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        Axeptio.shared.setupUI()
    }
```
}

### Object C
```objc
#import "ViewController.h"
@import AxeptioSDK;

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Initialize the UI elements required for consent display
    [Axeptio.shared setupUI];  // Ensure that this is called from your main view controller
}

@end
```
### Issues with the Consent Popup (Objective-C)
If the consent popup is not appearing as expected, follow these steps to troubleshoot and resolve the issue:

#### Ensure Correct SDK Initialization in AppDelegate:
Verify that the SDK is properly initialized in the `AppDelegate.m` file with the correct `clientId` and `cookiesVersion`
```objc
#import "AppDelegate.h"
@import AxeptioSDK;

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    AxeptioService targetService = AxeptioServiceBrands; // Or use AxeptioServicePublisherTcf if required

    // Initialize with the provided Client ID and Cookies Version
    [Axeptio.shared initializeWithTargetService:targetService
                                    clientId:@"<Your Client ID>"
                                cookiesVersion:@"<Your Cookies Version>"];

    // Optional: Initialize with a Token from another device
    [Axeptio.shared initializeWithTargetService:targetService
                                    clientId:@"<Your Client ID>"
                                cookiesVersion:@"<Your Cookies Version>"
                                          token:@"<Token>"];

    return YES;
}

@end
```
#### Correctly Calling `setupUI` from Main `UIViewController`:
Ensure that the `setupUI` method is called from your main view controller (usually in `viewDidLoad` or a similar lifecycle method) to properly trigger the consent popup display.
```objc
#import "ViewController.h"
@import AxeptioSDK;

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Call setupUI to show the consent popup when appropriate
    [Axeptio.shared setupUI];  
}

@end
```

#### Check for Potential UI Blockers
If the consent popup is not showing, check if other views or modals are blocking it. Temporarily disable any other views that might interfere with the consent view to ensure it is not being hidden.

#### Verify Event Logging for Popup Request:
Add a logging statement to confirm that the SDK is triggering the popup:
```objc
[Axeptio.shared setupUI];
NSLog(@"Consent popup triggered successfully");
```
#### Ensure Proper Event Listeners are Set Up
If you are using event listeners to capture actions like the consent popup being closed, ensure that they are properly implemented and assigned.
```objc
AxeptioEventListener *axeptioEventListener = [[AxeptioEventListener alloc] init];
[axeptioEventListener setOnPopupClosedEvent:^{
    NSLog(@"Consent popup closed by the user");
}];
[Axeptio.shared setEventListener:axeptioEventListener];
```
#### SDK Version
Ensure that you are using the latest version of the Axeptio SDK. Outdated versions might contain bugs that affect the popup behavior.

### SwiftUI Integration
#### Create a UIViewController subclass to call `setupUI()`
To integrate the Axeptio SDK into a SwiftUI app, first, create a subclass of `UIViewController` to invoke the SDK's `setupUI()` method. This view controller will later be integrated into SwiftUI using `UIViewControllerRepresentable`.
```swift
import SwiftUI
import AxeptioSDK

// Custom UIViewController to handle the SDK UI
class AxeptioViewController: UIViewController {

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)

        // Call the setupUI method of the SDK to show the consent popup
        Axeptio.shared.setupUI()
    }
}
```

#### Create a `UIViewControllerRepresentable` struct
Next, create a struct that conforms to the `UIViewControllerRepresentable` protocol to integrate the custom `UIViewController` into the SwiftUI view hierarchy. This struct will allow you to display the `AxeptioViewController` as a SwiftUI view.
```swift
// Struct to integrate AxeptioViewController into SwiftUI
struct AxeptioView: UIViewControllerRepresentable {

    // Create the custom UIViewController
    func makeUIViewController(context: Context) -> some UIViewController {
        return AxeptioViewController()
    }

    // Required method, but not used in this case
    func updateUIViewController(_ uiViewController: UIViewControllerType, context: Context) {}
}
```
#### Connect with the AppDelegate using `UIApplicationDelegateAdaptor`
In SwiftUI, to properly set up the application and initialize the SDK, you'll need an entry point that implements the initialization logic in the `AppDelegate`. Use `UIApplicationDelegateAdaptor` to connect your `AppDelegate` to the SwiftUI app structure.
```swift
import SwiftUI
import AxeptioSDK

// AppDelegate that initializes the SDK
class AppDelegate: NSObject, UIApplicationDelegate {

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil) -> Bool {
        
        // Initialize the Axeptio SDK with the Client ID and cookies version
        Axeptio.shared.initialize(clientId: "<Your Client ID>", cookiesVersion: "<Your Cookies Version>")

        return true
    }
}

// Main SwiftUI app structure
@main
struct YourSwiftUIApp: App {
    // Bind the AppDelegate to the SwiftUI app structure
    @UIApplicationDelegateAdaptor(AppDelegate.self) var appDelegate

    var body: some Scene {
        WindowGroup {
            // Display the AxeptioView which contains the custom UIViewController
            AxeptioView()
        }
    }
}
```
By following these steps, the Axeptio SDK will be correctly integrated into a SwiftUI app, and the logic for displaying the consent popup will be handled inside `viewDidAppear()` within the custom `UIViewController


