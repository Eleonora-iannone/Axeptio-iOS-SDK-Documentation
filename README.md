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
6. [Axeptio SDK and App Tracking Transparency (ATT) Integration](#axeptio-sdk-and-app-tracking-transparency-att-integration)
   - [Swift Integration](#swift-integration)
   - [Objective C Integration](#objective-c-integration)
7. [Responsibilities Mobile App vs SDK](#responsibilities-mobile-app-vs-sdk)
8. [Retrieving Stored Consents](#retrieving-stored-consents)
9. [Show Consent Popup on Demand](#show-consent-popup-on-demand)
10. [Clearing Consent from `UserDefaults`](#clearing-consent-from-userdefaults)
   
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
By following these steps, the Axeptio SDK will be correctly integrated into a SwiftUI app, and the logic for displaying the consent popup will be handled inside `viewDidAppear()` within the custom `UIViewController`

## Axeptio SDK and App Tracking Transparency (ATT) Integration

Starting with iOS 14.5, Apple introduced the App Tracking Transparency (ATT) framework, which requires apps to request user consent before tracking their data across other apps and websites. The Axeptio SDK does **not** automatically handle ATT permission requests, and it is your responsibility to ask for user consent for tracking and manage how the Axeptio Consent Management Platform (CMP) interacts with the ATT permission.

This steps will show you how to:

- Request ATT permission.
- Display the Axeptio consent notice after the user has accepted the ATT permission.
- Handle cases where ATT permission is not requested or denied, and show the Axeptio CMP accordingly.

## Overview

The Axeptio SDK does not ask for the user’s tracking permission using the ATT framework. It is your responsibility to request this permission, and the way in which the ATT framework and Axeptio CMP interact depends on your app's logic.

In apps targeting iOS 14.5 and above, you must use the `ATTrackingManager.requestTrackingAuthorization` function to ask for tracking consent. Based on the user’s response, you can choose to show the Axeptio consent notice.

### Expected Flow:

1. **ATT Permission**: Show the ATT permission dialog if the iOS version is 14 or later.
2. **Axeptio Consent Notice**: Show the Axeptio consent notice if:
   - iOS version is >= 15.
   - The user accepts the ATT permission.
3. **Fallback**: If the ATT permission cannot be displayed (e.g., restricted, iOS < 14, or user denied permission), you can still show the Axeptio CMP.

## Swift Integration

Below is the complete Swift code to handle the ATT permission and initialize the Axeptio CMP.

### Step 1: Request ATT Permission and Show Axeptio CMP

```swift
import UIKit
import AppTrackingTransparency
import AxeptioSDK

class ViewController: UIViewController {

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        Task {
            await handleATTAndInitializeAxeptioCMP()
        }
    }

    private func handleATTAndInitializeAxeptioCMP() async {
        if #available(iOS 14, *) {
            let status = await ATTrackingManager.requestTrackingAuthorization()
            let isAuthorized = (status == .authorized)
            initializeAxeptioCMPUI(granted: isAuthorized)
        } else {
            initializeAxeptioCMPUI(granted: true)  // ATT not required for iOS < 14
        }
    }

    private func initializeAxeptioCMPUI(granted: Bool) {
        if granted {
            // Initialize Axeptio CMP UI if ATT permission is granted
            Axeptio.shared.setupUI()
        } else {
            // Handle case where user denies permission or ATT is restricted
            Axeptio.shared.setUserDeniedTracking()
        }
    }
}
```
#### Key Points:
- `ATTrackingManager.requestTrackingAuthorization`: Requests permission for tracking and returns the status.
- `Axeptio.shared.setupUI()`: Initializes and shows the consent notice once ATT permission is granted.
- **Fallback Handling**: If ATT permission is denied or unavailable, the Axeptio CMP can still be initialized depending on your requirements (e.g., on iOS versions before 14).

#### iOS 14 and Above:
- ATT framework is only available for iOS 14 and later.
- If the app is running on iOS 14+, it will request the ATT permission.
- the user grants permission, you can show the Axeptio consent notice using `Axeptio.shared.setupUI()`.

### Objective C Integration
For Objective-C, the implementation is quite similar. You’ll request ATT permission and initialize the Axeptio CMP based on the user's response.
### Step 1: Request ATT Permission and Show Axeptio CMP

```objc
#import <AppTrackingTransparency/AppTrackingTransparency.h>
@import AxeptioSDK;

@implementation ViewController

- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
    
    if (@available(iOS 14, *)) {
        // Request ATT permission if on iOS >= 14
        [self requestTrackingAuthorization];
    } else {
        // Initialize Axeptio CMP if on iOS < 14 (ATT not required)
        [Axeptio.shared setupUI];
    }
}

- (void)requestTrackingAuthorization {
    [ATTrackingManager requestTrackingAuthorizationWithCompletionHandler:^(ATTrackingManagerAuthorizationStatus status) {
        BOOL isAuthorized = (status == ATTrackingManagerAuthorizationStatusAuthorized);
        [self initializeAxeptioCMPUI:isAuthorized];
    }];
}

- (void)initializeAxeptioCMPUI:(BOOL)granted {
    if (granted) {
        // Initialize Axeptio CMP UI if ATT permission is granted
        [Axeptio.shared setupUI];
    } else {
        // Handle case where user denies permission or ATT is restricted
        [Axeptio.shared setUserDeniedTracking];
    }
}

@end
```
#### Key Points:
- `ATTrackingManager.requestTrackingAuthorizationWithCompletionHandler`: This method requests ATT permission and provides a callback with the status of the request.
- `Axeptio.shared.setupUI()`: This method initializes and shows the consent notice after the user has granted ATT permission.
- **Fallback Handling**: Similar to the Swift implementation, you can still show the Axeptio CMP even if the ATT permission is not granted or not available.

#### Importante Notes:
- **ATT Request Flow**: The ATT request must be shown at an appropriate time in your app flow, typically when the user first opens the app or at a point where they can make an informed decision.
- **IOS 14+**: The ATT framework is only available on iOS 14 and later. For earlier versions of iOS, you can proceed with displaying the Axeptio consent notice without needing ATT permission.
- **Data Collection Disclosure**: Apple's App Store guidelines require you to disclose what data your app collects and how it uses it. Ensure your app’s privacy policy is up to date, and provide clear information on what data is being collected for tracking purposes.

#### Useful Links
- [Apple’s App Tracking Transparency Documentation](https://developer.apple.com/documentation/apptrackingtransparency)
- [Apple's App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)
- [Mobile App SDK](https://www.notion.so/axeptio/Mobile-App-SDK-1812c92d467c80dc86b3f190140c42e1#1822c92d467c80fcaf81f0811869b278)

### Responsibilities Mobile App vs SDK

The integration of the Axeptio SDK into your mobile application involves clear delineation of responsibilities between the mobile app and the SDK itself. Below are the distinct roles for each in handling user consent and tracking.

#### **Mobile Application Responsibilities:**

1. **Managing App Tracking Transparency (ATT) Flow:**
   - The mobile app is responsible for initiating and managing the ATT authorization process on iOS 14 and later. This includes presenting the ATT request prompt at an appropriate time in the app's lifecycle.

2. **Controlling the Display Sequence of ATT and CMP:**
   - The app must determine the appropriate sequence for displaying the ATT prompt and the Axeptio consent management platform (CMP). Specifically, the app should request ATT consent before invoking the Axeptio CMP.

3. **Compliance with App Store Privacy Labels:**
   - The app must ensure accurate and up-to-date declarations of data collection practices according to Apple’s privacy label requirements, ensuring full transparency to users about data usage.

4. **Event Handling and User Consent Updates:**
   - The app is responsible for handling SDK events such as user consent actions. Based on these events, the app must adjust its behavior accordingly, ensuring that user consent is respected across sessions.

#### **Axeptio SDK Responsibilities:**

1. **Displaying the Consent Management Interface:**
   - The Axeptio SDK is responsible for rendering the user interface for the consent management platform (CMP) once triggered. It provides a customizable interface for users to give or revoke consent.

2. **Storing and Managing User Consent Choices:**
   - The SDK securely stores and manages user consent choices, maintaining a persistent record that can be referenced throughout the app's lifecycle.

3. **Sending Consent Status via APIs:**
   - The SDK facilitates communication of the user's consent status through APIs, allowing the app to be updated with the user’s preferences.

4. **No Implicit Handling of ATT Permissions:**
   - The Axeptio SDK does **not** manage the App Tracking Transparency (ATT) permission flow. It is the host app's responsibility to request and handle ATT permissions explicitly before displaying the consent management interface. The SDK functions only once the ATT permission is granted (or bypassed due to platform restrictions).

### Retrieving Stored Consents

To retrieve user consent preferences stored by the Axeptio SDK, you can access the data stored in the `UserDefaults`. The SDK automatically stores consent information in `UserDefaults`, making it accessible for the app to retrieve whenever necessary.

#### **Retrieving Consents in Swift:**

In Swift, you can access the stored consents by using the `UserDefaults` API. This allows you to query specific consent keys, such as the one you previously stored when the user made their choices.

```swift
let consent = UserDefaults.standard.object(forKey: "Key")
```
This will return the consent data associated with the provided key. Ensure that you know the specific key associated with the consent data you're trying to access.

#### **Retrieving Consents in Objective-C:**
In Objective-C, you can access the stored consents using the `NSUserDefaults` class. The following code demonstrates how to retrieve the consent data stored in `NSUserDefaults`:
```objc
id consent = [[NSUserDefaults standardUserDefaults] objectForKey:@"Key"];
```
This will return the consent information associated with the specified key.

For a more detailed breakdown of how the Axeptio SDK handles stored consent values, including cookie management and other privacy-related data, please refer to the [Axeptio SDK Documentation](https://support.axeptio.eu/hc/en-gb/articles/8558526367249-Does-Axeptio-deposit-cookies).

### Show Consent Popup on Demand

You can request the consent popup to be displayed programmatically at any point in your app’s lifecycle. This can be useful when you need to show the consent screen after a specific user action or event, rather than automatically when the app starts.
- This method will display the consent management platform (CMP) UI based on the user's current consent status.
- Make sure to trigger the consent popup at the appropriate moment to avoid interrupting the user experience.
- The consent popup can be triggered even after the app has been launched and after the consent has already been obtained, allowing you to ask for consent again if necessary.

#### Swift Implementation:
To trigger the consent popup on demand in Swift, you can call the `showConsentScreen()` method on the `Axeptio.shared` instance:

```swift
Axeptio.shared.showConsentScreen()
```
#### Objective-C Implementation

Similarly, in Objective-C, the same method can be invoked to show the consent screen on demand:
```objc
[Axeptio.shared showConsentScreen];
```

### Clearing Consent from `UserDefaults`

A method is provided to clear the stored consent information from `UserDefaults`. This allows you to reset the user's consent status and remove any previously stored preferences.
- This method will remove the stored consent data, which may include preferences or other consent-related information stored in UserDefaults.
- It's useful for scenarios where the user needs to update their consent choices or when you want to reset consent state for any other reason.
- Once consent is cleared, the app may re-prompt the user for consent based on the current configuration or flow.

#### Swift Implementation:
To clear the consent from `UserDefaults` in Swift, simply invoke the `clearConsent()` method on the shared `Axeptio` instance:

```swift
Axeptio.shared.clearConsent()
```
#### Objective-C Implementation:
Similarly, in Objective-C, you can call the clearConsent method on the shared Axeptio instance to remove the stored consent:
```objc
[Axeptio.shared clearConsent];
```

