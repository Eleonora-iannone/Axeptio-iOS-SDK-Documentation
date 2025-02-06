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

- Open your 'Podfile' in the root directory of your project
- Add the following configuration:
  ```ruby
  source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '15.0'
use_frameworks!

target 'MyApp' do
  pod 'AxeptioIOSSDK'
end
```
