# iOS Development
## Table of Contents

| Section | Description |
|---------|-------------|
| [1. Project Setup](#1-project-setup) | React web app to iOS native app setup |
| [1.1 Dynamic Entry Point Configuration](#11-dynamic-entry-point-configuration) | Setting up dynamic entry points |
| [1.2 Modify Vite Config File](#12-modify-vite-config-file) | Updating Vite configuration |
| [1.3 Create Vite Config File for Mobile App](#13-create-vite-config-file-for-mobile-app) | Creating mobile-specific Vite config |
| [1.4 Create MobileAppProvide component](#14-create-mobileappprovide-component) | Setting up mobile app provider |
| [1.5 Create MobileApp component](#15-create-mobileapp-component) | Creating mobile app component |
| [1.6 Create mobMain component](#16-create-mobmain-component) | Setting up mobile entry point |
| [1.7 Add Capacitor packages](#17-add-capacitor-packages) | Adding required Capacitor dependencies |
| [2. Building Native iOS App](#2-building-native-ios-app) | Complete Capacitor.js installation guide |
| [2.1 Install Capacitor](#21-install-capacitor) | Adding core dependencies |
| [2.2 Add Configuration](#22-add-configuration) | Setting up app configuration |
| [2.3 Install iOS Platform](#23-install-ios-platform) | Adding iOS-specific dependencies |
| [2.4 Build the Web App and Run the iOS App](#24-build-the-web-app-and-run-the-ios-app) | Building and running the app |
| [2.5 Launch in X Code](#25-launch-in-x-code) | Opening project in Xcode |
| [2.6 Run Your App in Stimulator](#26-run-your-app-in-stimulator) | Running in iOS simulator |
| [3. Firebase Project Creation for Push Notification](#3-firebase-project-creation-for-push-notification) | Setting up Firebase for push notifications |
| [3.1 Add Push Notification and Remote Notification Capabilities](#31-add-push-notification-and-remote-notification-capabilities) | Adding notification capabilities |
| [3.2 Create Firebase Project](#32-create-firebase-project) | Setting up Firebase project |
| [3.3 Add Firebase to Your Apple App](#33-add-firebase-to-your-apple-app) | Integrating Firebase with iOS app |
| [4. Generating Apple Push Notification Keys for FCM](#4-generating-apple-push-notification-keys-for-fcm) | Setting up APNs keys for Firebase |
| [4.1 Prerequisites](#41-prerequisites) | Required accounts and access |
| [4.2 Steps to Generate Keys](#42-steps-to-generate-keys) | APNs key generation process |
| [4.3 Required Information](#43-required-information) | Key details and IDs |
| [4.4 Configure Firebase Project](#44-configure-firebase-project) | Firebase configuration steps |
| [5. Back Gesture](#5-back-gesture) | Implementing back gesture functionality |
| [6. Screen Orientation lock in Portrait](#6-screen-orientation-lock-in-portrait) | Configuring screen orientation |
| [7. Apple Privacy Manifest Requirements for file downloads](#7-apple-privacy-manifest-requirements-for-file-downloads) | Setting up privacy manifest |
| [8. Adding Deeplink](#8-adding-deeplink) | Setting up deep linking functionality |
| [8.1 Prerequisites](#81-prerequisites) | Required accounts and access |
| [8.2 Create Site Association File](#82-create-site-association-file) | Setting up site association |
| [8.3 Add Associated Domain Capabilitiy](#83-add-associated-domain-capabilitiy) | Adding domain capabilities |
| [9. App Update (OTA Update)](#9-app-update-ota-update) | Setting up over-the-air updates |
| [10. App Store Release Checklist](#10-app-store-release-checklist) | Pre-release verification steps |
| [10.1 VS Code Tasks](#101-vs-code-tasks) | VS Code preparation tasks |
| [10.2 Xcode Tasks](#102-xcode-tasks) | Xcode preparation tasks |
---------------------------------------------------------------------------------------------------
  # 1. Project Setup

### 1.1 Dynamic Entry Point Configuration

Enable a dynamic entry point for your project, by following these steps to modify the `index.html` file appropriately.

####  Modify `index.html`

1. **Navigate to** the root directory of your project and locate the `index.html` file.
2. **Update the script reference** inside the `<body>` tag by replacing the hardcoded script source with a dynamic placeholder.

3. **Before Modification**:
The default `index.html` contains a static script reference pointing to `main.tsx`:

```html
<!-- index.html -->
<body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script> <!-- ✅ Change pending implementation. -->
</body>
```

4. **After Modification**:
Replace the static script tag with the dynamic template placeholder `<%- injectMainScript %>:`

```html
<!-- index.html -->
<body>
    <div id="root"></div>
    <%- injectMainScript %> <!-- ✅ Change Made -->
</body>
```

### 1.2 Modify Vite Config File:

1. **Navigate to** the root directory of your project and locate the `vite.config.ts` file.
2. **Update** the **plugin** in `defineConfig` with the following code.

    **Before Modification**:
    Add this import:
    
    ```ts
    import { createHtmlPlugin } from 'vite-plugin-html';
    ```
    
    Then, update the `plugins` array:
    
    ```ts
    plugins: [react(), tsConfigPaths(), eslint({ fix: true })],
    ```

    **After Modification**:

    ```ts
    // vite.config.ts
    import { createHtmlPlugin } from 'vite-plugin-html';

    plugins: [
        react(),
        tsConfigPaths(),
        eslint({ fix: true }),
        createHtmlPlugin({
            inject: {
                data: {
                    injectMainScript: '<script type="module" src="./src/main.tsx"></script>',
                },
            },
            minify: true,
        }),
    ],
    ```

### 1.3 Create Vite Config File for Mobile App:

1. First, navigate to your project's root directory and create a new file called `vitemob.config.ts`.
2. From there, locate the existing `vite.config.ts` file in the same directory and duplicate its entire codebase into your newly created `vitemob.config.ts` file apply the following modifications.

    **vite.config.ts file**:

    ```ts
    // vite.config.ts
    plugins: [
        react(),
        tsConfigPaths(),
        eslint({ fix: true }),
        createHtmlPlugin({
            inject: {
                data: {
                    injectMainScript: '<script type="module" src="./src/main.tsx"></script>', // ✅ Replace this line 
                },
            },
            minify: true,
        }),
    ],
    ```

    **New vitemob.config.ts file**:

    ```ts
    // vitemob.config.ts
    plugins: [
        react(),
        tsConfigPaths(),
        eslint({ fix: true }),
        createHtmlPlugin({
            inject: {
                data: {
                    injectMainScript: '<script type="module" src="./src/mobMain.tsx"></script>', // ✅ Changes Made
                },
            },
            minify: true,
        }),
    ],
    ```


### 1.4 Create MobileAppProvide component:
1. First, navigate to your project's provider directory at src => packages => core => providers and create a new file called `MobileAppProvider.tsx`.

2. From there, locate the existing `app.tsx` file in the same directory and duplicate its entire codebase into your newly created `MobileAppProvider.tsx file`.

3. Within` MobileAppProvider.tsx`, you'll need to update the component naming convention - specifically, replace all instances of `AppProvider` with `MobileAppProvider` and `AppProviderProps` with `MobileAppProviderProps` to reflect its mobile-specific purpose.

4. Finally, integrate the new `MobileAppProvider` component by modifying the `AppCoreProvider.tsx` file according to the code snippets below:

    **Before Modification**:

    ```tsx
    // AppCoreProvider.tsx
    import BrowserOffline from '@/components/Custom/BrowserOffline';
    import Spinner from '@core/components/Elements/Spinner';
    import AppProvider from '@core/providers/app';
    import { actions } from '@core/store';
    import { Config } from '@core/types';
    import { RouterProvider } from 'react-router-dom';

    export const AppCoreProvider: React.FC<{ config: Config }> = ({ config }) => {
    actions.config.initialize(config);

    return (
        <AppProvider>
        <BrowserOffline />
        <RouterProvider router={config.router} fallbackElement={<Spinner />} />
        </AppProvider>
    );
    };
    ```

    **After Modification**:

    ```tsx 
    // AppCoreProvider.tsx
    import BrowserOffline from '@/components/Custom/BrowserOffline';
    import Spinner from '@core/components/Elements/Spinner';
    import AppProvider from '@core/providers/app';
    import { actions } from '@core/store';
    import { Config } from '@core/types';
    import { RouterProvider } from 'react-router-dom';
    import MobileAppProvider from './MobileAppProvider'; // ✅ New Line of code

    export const AppCoreProvider: React.FC<{ config: Config }> = ({ config }) => {
    actions.config.initialize(config);

    return (
        <AppProvider>
        <BrowserOffline />
        <RouterProvider router={config.router} fallbackElement={<Spinner />} />
        </AppProvider>
    );
    };

    // ✅ New block of code
    export const MobileAppCoreProvider: React.FC<{ config: Config }> = ({ config }) => {
    actions.config.initialize(config);

    return (
        <MobileAppProvider>
        <BrowserOffline />
        <RouterProvider router={config.router} fallbackElement={<Spinner />} />
        </MobileAppProvider>
    );
    };
    // ✅ New block of code
    ```

### 1.5 Create MobileApp component:

1. First, navigate to your project's `src` directory and create a new file called `MobileApp.tsx`.
2. From there, locate the existing `App.tsx` file in the same directory and duplicate its entire codebase into your newly created `MobileApp.tsx` file.
3. Within `MobileApp.tsx`, you'll need to update the component naming convention - specifically, replace all instances of `App` with `MobileApp` and import `MobileAppCoreProvider` component and use this component in place of `AppCoreProvider`.

    **App.tsx file**:

    ```tsx
    //App.tsx file
    import { AppCoreProvider } from '@core/providers';
    import { Config } from '@core/types';
    import { publicRoutes } from '@/routes/public';

    function App() {
    const config: Config = {
        apiUrl: import.meta.env.VITE_API_URL,
        wsUrl: import.meta.env.VITE_WS_URL,
        environment: import.meta.env.MODE,
        storageKeyPrefix: 'tzm-palm_',
        projectName: 'Palm Meadows',
        defaultRoute: '/app/home',
        router: publicRoutes,
    };
    return <AppCoreProvider config={config} />;
    }

    export default App;
    ```

    **New MobileApp.tsx file**:

    ```tsx
    // MobileApp.tsx file
    import { MobileAppCoreProvider } from '@core/providers/AppCoreProvider'; // ✅ Changes Made
    import { Config } from '@core/types';
    import { publicRoutes } from '@/routes/public';

    function MobileApp() {      // ✅ Changes Made
    const config: Config = {
        apiUrl: import.meta.env.VITE_API_URL,
        wsUrl: import.meta.env.VITE_WS_URL,
        environment: import.meta.env.MODE,
        storageKeyPrefix: 'tzm-palm_',
        projectName: 'Palm Meadows',
        defaultRoute: '/app/home',
        router: publicRoutes,
    };
    return <MobileAppCoreProvider config={config} />; // ✅ Changes Made
    }

    export default MobileApp; // ✅ Changes Made

    ```


### 1.6 Create mobMain component:

1. First, navigate to your project's `src` directory and create a new file called `mobMain.tsx`.
2. From there, locate the existing `main.tsx` file in the same directory and duplicate its entire codebase into your newly created `mobMain.tsx` file.
3. Within `mobMain.tsx`, made the following changes.

    **main.tsx file**:
    ```tsx 
    // main.tsx 
    import '@core/utils/wdyr';
    import React from 'react';
    import ReactDOM from 'react-dom/client';
    import 'leaflet/dist/leaflet.css';
    import './index.css';
    import { ApolloProvider } from '@apollo/client';
    import client from '@core/lib/apolloClient/apolloClient';

    const App = React.lazy(() => import('./App')); // ✅ Change pending implementation.

    ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
    <React.StrictMode>
        <ApolloProvider client={client}>
        <App />  // ✅ Change pending implementation.
        </ApolloProvider>
    </React.StrictMode>

    );
    ```

    **New mobMain.tsx file**:

    ```tsx 
    // mobMain.tsx 
    import '@core/utils/wdyr';
    import React from 'react';
    import ReactDOM from 'react-dom/client';
    import 'leaflet/dist/leaflet.css';
    import './index.css';
    import { ApolloProvider } from '@apollo/client';
    import client from '@core/lib/apolloClient/apolloClient';

    const MobileApp = React.lazy(() => import('./MobileApp'));  // ✅ Changes Made

    ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
    <React.StrictMode>
        <ApolloProvider client={client}>
        <MobileApp />  // ✅ Changes Made
        </ApolloProvider>
    </React.StrictMode>
    );
    ```
### 1.7 Add Capacitor packages
1. Add the required capacitor packages in the `package.json` file's **dependencies**.
    ```json
    "@apollo/client": "^3.10.6",
    "@capacitor/android": "^7.0.0",
    "@capacitor/app": "^7.0.0",
    "@capacitor/cli": "^7.0.0",
    "@capacitor/core": "^7.0.0",
    "@capacitor/filesystem": "^7.0.0",
    "@capacitor/ios": "^7.0.0",
    "@capacitor/local-notifications": "^7.0.0",
    "@capacitor/network": "^7.0.0",
    "@capacitor/push-notifications": "^7.0.0",
    "@capacitor/screen-orientation": "^7.0.0",
    "@capacitor/storage": "^1.2.5",
    "@capacitor/toast": "^7.0.0",
    "@capawesome-team/capacitor-file-opener": "7.0.1",
    "@capgo/capacitor-updater": "^7.0.2",
    ```
  2. Add the required scripts.
      ```json
      "mob-build": "tsc &&  NODE_OPTIONS='--max-old-space-size=4096' vite build --mode development --config vitemob.config.ts",
      "mob-build-stage": "tsc && NODE_OPTIONS='--max-old-space-size=4096' vite build --mode staging --config vitemob.config.ts",
      "mob-build-prod": "tsc && NODE_OPTIONS='--max-old-space-size=4096' vite build --mode production --config vitemob.config.ts",
      "open-android": "npx cap open android",
      "run-android": "cap sync && cap update android && cap run android",
      "android-update": "cap sync && cap update android",
      "app": "cap sync && cap update android && cap run android && cap open android",
      "ios": "cap sync && cap update ios && cap run ios && cap open ios"
        ```

## 2. Building Native iOS App

Website Reference: [https://capacitorjs.com/solution/react](https://capacitorjs.com/solution/react)

### 2.1 Install Capacitor

Add Capacitor core dependencies to your project:

```bash
# Install core dependencies
yarn add @capacitor/core @capacitor/cli
```

### 2.2 Add Configuration

Initialize Capacitor configuration for your app:

```bash
# Initialize Capacitor with app details
npx cap init [name] [id] --web-dir=build
```

**Configuration parameters:**
- **name**: Your app's human-readable name (e.g., "Palm Meadows")
- **id**: Unique identifier in reverse-domain format (e.g., "com.palmmeadows.thingZmate")
- **--web-dir**: Directory containing your built web assets (typically "build")

### 2.3 Install iOS Platform

Add iOS-specific dependencies and platform:

```bash
# Add iOS platform dependencies
yarn add @capacitor/ios

# Initialize iOS platform
npx cap add ios
```

### 2.4 Build the Web App and Run the iOS App

First, add this script to your `package.json`:

```json
{
  "scripts": {
    "ios": "cap sync && cap update ios && cap run ios"
  }
}
```

Then build and launch your app:

```bash
# Build web assets and launch iOS app
yarn build
yarn ios
```

### 2.5 Launch in X Code

Open the app in X code:

```bash
# Open project in Xcode
npx cap open ios
```

### 2.6 Run Your App in Stimulator

1. Open Xcode and go to **Window => Devices and Simulators** to access the iOS Simulator.
2. Select a desired device model from the available list.
3. In Xcode, navigate to the top toolbar and click the **Play button (▶)** located in the upper-left corner, or press **Cmd + R** to build and run the app in the selected simulator.

## 3. Firebase Project Creation for Push Notification

### 3.1 Add Push Notification and Remote Notification Capabilities

Website Reference: [Adding capabilities to your app](https://developer.apple.com/documentation/xcode/adding-capabilities-to-your-app)

1. Open your app in Xcode. In the Project navigator of the main window, select the project and click the Signing & Capabilities tab.

2. Click the Library button (+) to open the Capabilities library.

3. Search for **Background modes** and add it to the app target.

4. Search and add **Push Notification** Capability.

5. In **Background modes**, select **remote notification** options.

### 3.2 Create Firebase Project

1. Open [Firebase Console](https://console.firebase.google.com/) (Login with thingsmateets@gmail.com account)

2. Click **Create a project** in the Firebase console.

3. Enter project name and click **Continue**.

4. Choose `Default account for Firebase` and click **Create Project**.

### 3.3 Add Firebase to Your Apple App

1. **Register Your iOS App with Firebase**
   - Click the **iOS** icon in Firebase console
   - Enter your iOS app's bundle ID
   - Provide app nickname (optional)
   - Click **Register App**

2. **Download the `GoogleService-Info.plist` File**
   - Download the configuration file
   - Add the `GoogleService-Info.plist` file you just downloaded into the root of your Xcode project and add it to `all targets`.

3. **Install Firebase SDK Using Swift Package Manager**
   - Navigate to **File > Add Packages**
   - Enter URL: `https://github.com/firebase/firebase-ios-sdk`
   - Select SDK version
   - Select **Pods** in `Add to projects`

4. **Initialize Firebase in Your App**

5. Add the following code to `AppDelegate.swift` without replacing the existing code, simply insert the new code in the appropriate locations, preserving the structure of the existing methods.

    ```swift
    // Add to AppDelegate.swift
    import Firebase
    import FirebaseMessaging

    func application(_ application: UIApplication, 
                    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        FirebaseApp.configure()  // ✅ Newly added code to initialize Firebase
        return true
    }
    // ✅ Newly added code to initialize Firebase
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        NotificationCenter.default.post(name: .capacitorDidRegisterForRemoteNotifications, object: deviceToken)
    }
    // ✅ Newly added code to initialize Firebase
    func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        NotificationCenter.default.post(name: .capacitorDidFailToRegisterForRemoteNotifications, object: error)
    }
    ```

## 4. Generating Apple Push Notification Keys for FCM

### 4.1 Prerequisites
- Apple Developer Account with admin access
- Access to Apple Developer Portal
- Firebase project set up

### 4.2 Steps to Generate Keys

1. **Access Apple Developer Portal**
   - Visit [Apple Developer Portal](https://developer.apple.com/account)
   - Sign in with your Apple Developer Account
   - Navigate to **"Certificates, Identifiers & Profiles"**

2. **Create Key for APNs**
   - Click **"Keys"** in the sidebar
   - Click the **"+"** button
   - Enter key name (e.g., *"FCM Push Notifications Key for Palm"*)
   - Select **"Apple Push Notifications service (APNs)"** checkbox
   - Click **"Continue"**
   - Review and click **"Register"**
   - Download key file (`.p8` format)

   > **IMPORTANT:** Store this file securely. You can only download it once!

### 4.3 Required Information
- **Key ID** (displayed on key details page)
- **Team ID** (found in top right of developer account page)

### 4.4 Configure Firebase Project
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Navigate to **Project Settings**
3. Select **"Cloud Messaging"** tab
4. In **"Apple app configuration"** section go to **APNs Authentication Key**:
   - Upload `.p8` key file
   - Enter **Key ID**
   - Enter **Team ID**
5. Click **"Save"**

Refer `useLogin.ts`, `PushNotificationListeners.tsx` and `LocalNotificationListeners.tsx` in **Palm Meadows** project.

## 5. Back Gesture

1. Create a **MyViewController.swift** file by opening `Xcode`, right-clicking on the App group (under the App target), selecting **File => New => File from Template...** from the context menu, choosing **Cocoa Touch Class** in the window, set the Subclass of: `to UIViewController` in the next screen, and save the file.

2. Next, select the `Main.storyboard` file in the Project Navigator, select the **Bridge View Controller** in the **Bridge View Controller Scene**, select the Identity Inspector on the right, and change the name of the custom class to `MyViewController`.

3. Finally, select the `MyViewController.swift` file in the Project Navigator and replace that with the below code.


    ```swift
    import UIKit
    import Capacitor

    class MyViewController: CAPBridgeViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        webView!.allowsBackForwardNavigationGestures = false
        
        let backGesture = UIScreenEdgePanGestureRecognizer(target: self, action: #selector(handleBackGesture(_:)))
        backGesture.edges = .left
        webView?.addGestureRecognizer(backGesture)
    }

    @objc func handleBackGesture(_ gesture: UIScreenEdgePanGestureRecognizer) {
        if gesture.state == .recognized {
            // Send message to JavaScript to handle back navigation logic
            bridge?.triggerJSEvent(eventName: "backGesture", target: "window", data: "")
        }
    }

    }
    ```
4. This will register a **backGesture** event whenever back gesture is used in iOS and with respect to that event you can navigate back. Refer ``GoBackListener.tsx`` and `BackGestureListenerIos.tsx` in **Palm Meadows** project.

## 6. Screen Orientation lock in Portrait

1. Add the following code in the `AppDelegate.swift` file.

    ```swift
    func application(_ application: UIApplication, supportedInterfaceOrientationsFor window: UIWindow?) -> UIInterfaceOrientationMask {
    return UIInterfaceOrientationMask(rawValue: (self.window!.rootViewController as! CAPBridgeViewController).supportedInterfaceOrientations.rawValue)
    }
    ```
2. If you need to lock orientation on an **iPad** set the option Requires **Full Screen to YES** by adding the following to Info.plist.This can be directly added in **VS code**.

    ```swift 
    <key>UIRequiresFullScreen</key>
    <true/>
    ```

3. Refer `LockScreenOrientation.tsx` file in **Palm Meadows** project for implementing this feature. 

## 7. Apple Privacy Manifest Requirements for file downloads

1. To create `Privacy Manifest` file open Xcode then, **File => New => File from Template**.
2. Scroll down to the **Resource** section and select `App Privacy` File type and **Click Next**.
3. Check your app in the `Targets list` and **Click Create**.
4. This will create a `PrivacyInfo.xcprivacy` file.
5. Finally, select the `PrivacyInfo.xcprivacy` file in the Project Navigator and replace that with the below code.

    ```swift
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>NSPrivacyAccessedAPITypes</key>
        <array>
        <!-- Add this dict entry to the array if the PrivacyInfo file already exists -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryFileTimestamp</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
            <string>C617.1</string>
            </array>
        </dict>
        </array>
    </dict>
    </plist>
    ```
## 8. Adding Deeplink

### 8.1 Prerequisites
- Apple Developer Account with admin access
- Access to Apple Developer Portal

### 8.2 Create Site Association File
1. Visit [https://developer.apple.com/account](https://developer.apple.com/account).
2. Sign in with your Apple Developer Account.
3. Navigate to **"Certificates, Identifiers & Profiles"**.
4. Click **Identifiers**.
5. Select your app's identifier and note **Team ID** and **Bundle ID**, and under Capabilities, toggle **Associated Domains** then save.
6. Create the site association file (Filename: `apple-app-site-association`) **Note**: Do not save it with a file extension.
    ```json
    {
    "applinks": {
        "apps": [],
        "details": [
        {
            "appID": "TEAMID.BUNDLEID", // ✅ Replace Team ID and Bundle ID
            "paths": ["*"]
        }
        ]
    }
    }
    ```
7. Be sure to replace **TEAMID.BUNDLEID** with your own IDs example: `8L65AZE66A.palm.thingzmate.com`.
8. Next, upload the file to your web site **(hosted on HTTPS)**. The URL should follow this format: `https://palm.thingzmate.com/.well-known/apple-app-site-association`
9. Re

### 8.3 Add Associated Domain Capabilitiy

Website Reference: [Adding capabilities to your app](https://developer.apple.com/documentation/xcode/adding-capabilities-to-your-app)

1. Open your app in Xcode. In the Project navigator of the main window, select the project and click the Signing & Capabilities tab.

2. Click the Library button (+) to open the Capabilities library.

3. Search for **Associated Domain** and add it to the app target.

4. In the Domains entry that appears, edit it using the format **applinks:yourdomain.com**. Example: `applinks:palm.thingzmate.com`.

Refer ``DeepLinkUrlListener.tsx``  in **Palm Meadows** project for implementing this feature.

# 9. App Update (OTA Update)
**Reference:** [https://github.com/Cap-go/capacitor-updater?tab=readme-ov-file](https://github.com/Cap-go/capacitor-updater?tab=readme-ov-file).
**NOTE**: The Over-the-Air (OTA) update feature is applicable only for modifications made to **HTML, CSS, and JavaScript files**. If you make any **changes** to the **native code**, such as **updates to Capacitor plugins,** it is mandatory to **resubmit** the application to the **app store** for approval.

1. If you have already created `privacy manifest` file just add the following dictionary key and the recommended reason to the `privacy manifest` file.

    ```swift
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryUserDefaults</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
            <string>CA92.1</string>
            </array>
        </dict>
    ```
    **Before Modification:**
     ```swift
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>NSPrivacyAccessedAPITypes</key>
        <array>
        <!-- Add this dict entry to the array if the PrivacyInfo file already exists -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryFileTimestamp</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
            <string>C617.1</string>
            </array>
        </dict>
        </array>
    </dict>
    </plist>
    ```
    **After Modification:**

    ```swift
        <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>NSPrivacyAccessedAPITypes</key>
        <array>
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryFileTimestamp</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
            <string>C617.1</string>
            </array>
        </dict>
        // ✅ New block of code
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryUserDefaults</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
            <string>CA92.1</string>
            </array>
        </dict>
        // ✅ New block of code
        </array>
    </dict>
    </plist>

    ```



2. If you do not have a `privacy manifest` file refer [7. Apple Privacy Manifest Requirements for file downloads](#7-apple-privacy-manifest-requirements-for-file-downloads) to create one and replace the following code in that file.

    ```Java
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>NSPrivacyAccessedAPITypes</key>
        <array>
        <!-- Add this dict entry to the array if the file already exists. -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryUserDefaults</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
            <string>CA92.1</string>
            </array>
        </dict>
        </array>
    </dict>
    </plist>
    ```
3.  Refer ``AppUpgrader.tsx``  in **Palm Meadows** project for implementing this feature.

# 10. App Store Release Checklist

## 10.1 VS Code Tasks  
1. **Comment out** the `server` property in `capacitor.config.ts`.  
2. Ensure the build is a **production build**.  
3. Update the **app version number** in `appVersion.ts`.  
4. Sync the web project with native by running:  
   ```sh
   yarn ios
   ```

## 10.2 Xcode Tasks 
1. Increase the **Version** and **Build** Number in the **General → Identity section**.
2. Ensure the same version number is used in **App Store Connect**.
3. Provide a clear and concise release **description** in App Store Connect.