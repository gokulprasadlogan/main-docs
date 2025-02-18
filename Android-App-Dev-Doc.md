| Section                                                                                                  | Description                                                      |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| [1. Project Setup](#1-project-setup)                                                                     | React web app to iOS native app setup                            |
| [1.1 Dynamic Entry Point Configuration](#11-dynamic-entry-point-configuration)                           | Setting up dynamic entry points                                  |
| [1.2 Modify Vite Config File](#12-modify-vite-config-file)                                               | Updating Vite configuration                                      |
| [1.3 Create Vite Config File for Mobile App](#13-create-vite-config-file-for-mobile-app)                 | Creating mobile-specific Vite config                             |
| [1.4 Create MobileAppProvide component](#14-create-mobileappprovide-component)                           | Setting up mobile app provider                                   |
| [1.5 Create MobileApp component](#15-create-mobileapp-component)                                         | Creating mobile app component                                    |
| [1.6 Create mobMain component](#16-create-mobmain-component)                                             | Setting up mobile entry point                                    |
| [1.7 Add Capacitor Packages](#17-add-capacitor-packages)                                                 | Adding required Capacitor dependencies                           |
| [2. Building Native Android App](#2-building-native-android-app)                                         | Complete Capacitor.js installation guide                         |
| [2.1 Install Capacitor](#21-install-capacitor)                                                           | Adding core dependencies                                         |
| [2.2 Add Configuration](#22-add-configuration)                                                           | Setting up app configuration                                     |
| [2.3 Install Android Platform](#23-install-android-platform)                                             | Adding Android-specific dependencies                             |
| [2.4 Build and Run the Android App](#24-build-and-run-the-android-app)                                   | Building and running the app                                     |
| [2.5 Launch in Android Studio](#25-launch-in-android-studio)                                             | Opening project in Android Studio                                |
| [2.6 Run Your App in Emulator](#26-run-your-app-in-emulator)                                             | Running in Android emulator                                      |
| [2.7 Run Your App in Android Physical Device](#27-run-your-app-in-android-physical-device)               | Testing on physical device                                       |
| [2.8 Generating Keystore File](#28-generating-keystore-file)                                             | Creating keystore for app signing                                |
| [3. Firebase Project Creation for Push Notification](#3-firebase-project-creation-for-push-notification) | Setting up Firebase for push notifications                       |
| [3.1 Create Firebase Project](#31-create-firebase-project)                                               | Creating new Firebase project                                    |
| [3.2 Add Firebase to Your Android App](#32-add-firebase-to-your-android-app)                             | Integrating Firebase with Android app                            |
| [3.3 Configure Firebase Project](#33-configure-firebase-project)                                         | Configuring Firebase settings                                    |
| [4. Listeners](#4-listeners)                                                                             | Setting up app listeners                                         |
| [5. Edge-To-Edge Enforcement on API 35](#5-edge-to-edge-enforcement-on-api-35)                           | Managing window insets and system bar interactions in Android 15 |

# 1. Project Setup

### 1.1 Dynamic Entry Point Configuration

Enable a dynamic entry point for your project, by following these steps to modify the `index.html` file appropriately.

#### Modify `index.html`

1. **Navigate to** the root directory of your project and locate the `index.html` file.
2. **Update the script reference** inside the `<body>` tag by replacing the hardcoded script source with a dynamic placeholder.

3. **Before Modification**:
   The default `index.html` contains a static script reference pointing to `main.tsx`:

```html
<!-- index.html -->
<body>
  <div id="root"></div>
  <script type="module" src="/src/main.tsx"></script>
  <!-- ✅ Change pending implementation. -->
</body>
```

4. **After Modification**:
   Replace the static script tag with the dynamic template placeholder `<%- injectMainScript %>:`

```html
<!-- index.html -->
<body>
  <div id="root"></div>
  <%- injectMainScript %>
  <!-- ✅ Change Made -->
</body>
```

### 1.2 Modify Vite Config File:

1. **Navigate to** the root directory of your project and locate the `vite.config.ts` file.
2. **Update** the **plugin** in `defineConfig` with the following code.

   **Before Modification**:
   Add this import:

   ```ts
   import { createHtmlPlugin } from "vite-plugin-html";
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
   import BrowserOffline from "@/components/Custom/BrowserOffline";
   import Spinner from "@core/components/Elements/Spinner";
   import AppProvider from "@core/providers/app";
   import { actions } from "@core/store";
   import { Config } from "@core/types";
   import { RouterProvider } from "react-router-dom";

   export const AppCoreProvider: React.FC<{ config: Config }> = ({
     config,
   }) => {
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
   import BrowserOffline from "@/components/Custom/BrowserOffline";
   import Spinner from "@core/components/Elements/Spinner";
   import AppProvider from "@core/providers/app";
   import { actions } from "@core/store";
   import { Config } from "@core/types";
   import { RouterProvider } from "react-router-dom";
   import MobileAppProvider from "./MobileAppProvider"; // ✅ New Line of code

   export const AppCoreProvider: React.FC<{ config: Config }> = ({
     config,
   }) => {
     actions.config.initialize(config);

     return (
       <AppProvider>
         <BrowserOffline />
         <RouterProvider router={config.router} fallbackElement={<Spinner />} />
       </AppProvider>
     );
   };

   // ✅ New block of code
   export const MobileAppCoreProvider: React.FC<{ config: Config }> = ({
     config,
   }) => {
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
   import { AppCoreProvider } from "@core/providers";
   import { Config } from "@core/types";
   import { publicRoutes } from "@/routes/public";

   function App() {
     const config: Config = {
       apiUrl: import.meta.env.VITE_API_URL,
       wsUrl: import.meta.env.VITE_WS_URL,
       environment: import.meta.env.MODE,
       storageKeyPrefix: "tzm-palm_",
       projectName: "Palm Meadows",
       defaultRoute: "/app/home",
       router: publicRoutes,
     };
     return <AppCoreProvider config={config} />;
   }

   export default App;
   ```

   **New MobileApp.tsx file**:

   ```tsx
   // MobileApp.tsx file
   import { MobileAppCoreProvider } from "@core/providers/AppCoreProvider"; // ✅ Changes Made
   import { Config } from "@core/types";
   import { publicRoutes } from "@/routes/public";

   function MobileApp() {
     // ✅ Changes Made
     const config: Config = {
       apiUrl: import.meta.env.VITE_API_URL,
       wsUrl: import.meta.env.VITE_WS_URL,
       environment: import.meta.env.MODE,
       storageKeyPrefix: "tzm-palm_",
       projectName: "Palm Meadows",
       defaultRoute: "/app/home",
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
   import "@core/utils/wdyr";
   import React from "react";
   import ReactDOM from "react-dom/client";
   import "leaflet/dist/leaflet.css";
   import "./index.css";
   import { ApolloProvider } from "@apollo/client";
   import client from "@core/lib/apolloClient/apolloClient";

   const App = React.lazy(() => import("./App")); // ✅ Change pending implementation.

   ReactDOM.createRoot(document.getElementById("root") as HTMLElement).render(
     <React.StrictMode>
       <ApolloProvider client={client}>
         <App /> // ✅ Change pending implementation.
       </ApolloProvider>
     </React.StrictMode>
   );
   ```

   **New mobMain.tsx file**:

   ```tsx
   // mobMain.tsx
   import "@core/utils/wdyr";
   import React from "react";
   import ReactDOM from "react-dom/client";
   import "leaflet/dist/leaflet.css";
   import "./index.css";
   import { ApolloProvider } from "@apollo/client";
   import client from "@core/lib/apolloClient/apolloClient";

   const MobileApp = React.lazy(() => import("./MobileApp")); // ✅ Changes Made

   ReactDOM.createRoot(document.getElementById("root") as HTMLElement).render(
     <React.StrictMode>
       <ApolloProvider client={client}>
         <MobileApp /> // ✅ Changes Made
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

## 2. Building Native Android App

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

### 2.3 Install Android Platform

Add iOS-specific dependencies and platform:

```bash
# Add android platform dependencies
yarn add @capacitor/android

# Initialize android platform
npx cap add android
```

### 2.4 Build and Run the Android App

Build and launch your app:

```bash
# Build web assets and launch iOS app
yarn build
yarn app
```

### 2.5 Launch in Android Studio

Open the app in Android Studio:

```bash
# Open project in Android Studio
npx cap open android
```

### 2.6 Run Your App in Emulator

1. Open **Android Studio**.
2. Click on **Tools** > **Device Manager**.
3. Click + icon and click **Create a Virtual Device**.
4. Select a device type (e.g., **Pixel 6**, **Nexus 5X**).
5. Click **Next**.
6. Select a recommended **API Level**.
7. Click **Next**.
8. Keep default settings or adjust as needed.
9. Click **Finish**.
10. Open your Android project in Android Studio.
11. Click **Run** ( ▶️ ) in the top menu.
12. Select the emulator you just created.
13. Click **OK**, and your app will launch on the emulator.

### 2.7 Run Your App in Android physical Device

1. Enable **Developer Options** on your phone.
2. Enable **USB Debugging**.
3. Connect via **USB** or **Wi-Fi (Both mobile and computer should be connected with same network)**.
4. Choose **Pair device using Using Wi-Fi**
5. Run the app on the connected device.

### 2.8 Generating Keystore file.

1. Open **Android Studio**.
2. Click on **Build** > **Generate signed app bundle or APK**.
3. Select **Android App Bundle or APK** and click **Next**.
4. In keystore path click **create new** and fill out the required details.
5. This will create a keystore file which will be used for next app updates.
   **Note**: The `keystore` file and its `credentials` are **extremely important**. Without them, **updating** the app will not be possible. Under no circumstances should the keystore file be **deleted or replaced** with a new one after the app has been released on the **Play Store.**

## 3. Firebase Project Creation for Push Notification

### 3.1 Create Firebase Project

1. Open [Firebase Console](https://console.firebase.google.com/) (Login with thingsmateets@gmail.com account)

2. Click **Create a project** in the Firebase console.

3. Enter project name and click **Continue**.

4. Choose `Default account for Firebase` and click **Create Project**.

### 3.2 Add Firebase to Your Android App

1. **Register Your Android App with Firebase**

- Click the **Android** icon in Firebase console
- Enter your Android app's bundle ID
- Provide app nickname (optional)
- Click **Register App**

2. **Download the `google-services.json` File**

- Download the configuration file
- Add your downloaded google-services.json file
  into your module (app-level) root directory.

- Click **Next**

3.  **Add FireBase SDK**

- Add the following code in the dependencies to your **project-level** `build.gradle` file:

  ```swift
  classpath 'com.google.gms:google-services:4.4.2'
  ```

  **Before Modification**

  ```swift
  buildscript {
      repositories {
          google()
          mavenCentral()
      }
      dependencies {
          classpath 'com.android.tools.build:gradle:8.7.3'
          // NOTE: Do not place your application dependencies here; they belong
          // in the individual module build.gradle files
      }
  }

  apply from: "variables.gradle"

  allprojects {
      repositories {
          google()
          mavenCentral()
      }
  }

  task clean(type: Delete) {
      delete rootProject.buildDir
  }

  ```

  **After Modification**

  ```swift
  buildscript {
      repositories {
          google()
          mavenCentral()
      }
      dependencies {
          classpath 'com.android.tools.build:gradle:8.7.3'
          classpath 'com.google.gms:google-services:4.4.2' // ✅ Newly added code to initialize Firebase

          // NOTE: Do not place your application dependencies here; they belong
          // in the individual module build.gradle files
      }
  }

  apply from: "variables.gradle"

  allprojects {
      repositories {
          google()
          mavenCentral()
      }
  }

  task clean(type: Delete) {
      delete rootProject.buildDir
  }

  ```

- Then, in your module **(app-level)** `build.gradle` file, add the following code

  **Before Modification**

  ```swift
  apply plugin: 'com.android.application'
  apply plugin: 'com.google.gms.google-services'

  android {
      namespace "com.thingzmate.palm"
      compileSdk rootProject.ext.compileSdkVersion
      defaultConfig {
          applicationId "com.thingzmate.palm"
          minSdkVersion rootProject.ext.minSdkVersion
          targetSdkVersion rootProject.ext.targetSdkVersion
          versionCode 5
          versionName "1.3.0"
          testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
          aaptOptions {
              // Files and dirs to omit from the packaged assets dir, modified to accommodate modern web apps.
              // Default: https://android.googlesource.com/platform/frameworks/base/+/282e181b58cf72b6ca770dc7ca5f91f135444502/tools/aapt/AaptAssets.cpp#61
              ignoreAssetsPattern '!.svn:!.git:!.ds_store:!*.scc:.*:!CVS:!thumbs.db:!picasa.ini:!*~'
          }
      }
      signingConfigs {
          release {
              storeFile file("../key/key_Palm")
              storePassword "PALM-meadows@2024"
              keyAlias "keyPalm-Meadows"
              keyPassword "PALM-meadows@2024"
          }
      }
      buildTypes {
          release {
              signingConfig signingConfigs.release
              minifyEnabled false
              proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
          }
      }
  }

  repositories {
      flatDir{
          dirs '../capacitor-cordova-android-plugins/src/main/libs', 'libs'
      }
  }

  dependencies {
      implementation fileTree(include: ['*.jar'], dir: 'libs')
      implementation "androidx.appcompat:appcompat:$androidxAppCompatVersion"
      implementation "androidx.coordinatorlayout:coordinatorlayout:$androidxCoordinatorLayoutVersion"
      implementation "androidx.core:core-splashscreen:$coreSplashScreenVersion"
      implementation project(':capacitor-android')
      testImplementation "junit:junit:$junitVersion"
      androidTestImplementation "androidx.test.ext:junit:$androidxJunitVersion"
      androidTestImplementation "androidx.test.espresso:espresso-core:$androidxEspressoCoreVersion"
      implementation project(':capacitor-cordova-android-plugins')
      implementation platform('com.google.firebase:firebase-bom:33.6.0')
      implementation 'com.google.firebase:firebase-messaging'

  }

  apply from: 'capacitor.build.gradle'

  try {
      def servicesJSON = file('google-services.json')
      if (servicesJSON.text) {
          apply plugin: 'com.google.gms.google-services'
      }
  } catch(Exception e) {
      logger.info("google-services.json not found, google-services plugin not applied. Push Notifications won't work")
  }

  ```

  **After Modification**

  ```swift
  apply plugin: 'com.android.application'
  apply plugin: 'com.google.gms.google-services' // ✅ Newly added code to initialize Firebase

  android {
      namespace "com.thingzmate.palm"
      compileSdk rootProject.ext.compileSdkVersion
      defaultConfig {
          applicationId "com.thingzmate.palm"
          minSdkVersion rootProject.ext.minSdkVersion
          targetSdkVersion rootProject.ext.targetSdkVersion
          versionCode 5
          versionName "1.3.0"
          testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
          aaptOptions {
              // Files and dirs to omit from the packaged assets dir, modified to accommodate modern web apps.
              // Default: https://android.googlesource.com/platform/frameworks/base/+/282e181b58cf72b6ca770dc7ca5f91f135444502/tools/aapt/AaptAssets.cpp#61
              ignoreAssetsPattern '!.svn:!.git:!.ds_store:!*.scc:.*:!CVS:!thumbs.db:!picasa.ini:!*~'
          }
      }
      signingConfigs {
          release {
              storeFile file("../key/key_Palm")
              storePassword "PALM-meadows@2024"
              keyAlias "keyPalm-Meadows"
              keyPassword "PALM-meadows@2024"
          }
      }
      buildTypes {
          release {
              signingConfig signingConfigs.release
              minifyEnabled false
              proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
          }
      }
  }

  repositories {
      flatDir{
          dirs '../capacitor-cordova-android-plugins/src/main/libs', 'libs'
      }
  }

  dependencies {
      implementation fileTree(include: ['*.jar'], dir: 'libs')
      implementation "androidx.appcompat:appcompat:$androidxAppCompatVersion"
      implementation "androidx.coordinatorlayout:coordinatorlayout:$androidxCoordinatorLayoutVersion"
      implementation "androidx.core:core-splashscreen:$coreSplashScreenVersion"
      implementation project(':capacitor-android')
      testImplementation "junit:junit:$junitVersion"
      androidTestImplementation "androidx.test.ext:junit:$androidxJunitVersion"
      androidTestImplementation "androidx.test.espresso:espresso-core:$androidxEspressoCoreVersion"
      implementation project(':capacitor-cordova-android-plugins')
      implementation platform('com.google.firebase:firebase-bom:33.6.0') // ✅ Newly added code to initialize Firebase
      implementation 'com.google.firebase:firebase-messaging' // ✅ Newly added code to initialize Firebase

  }

  apply from: 'capacitor.build.gradle'

  try {
      def servicesJSON = file('google-services.json')
      if (servicesJSON.text) {
          apply plugin: 'com.google.gms.google-services'
      }
  } catch(Exception e) {
      logger.info("google-services.json not found, google-services plugin not applied. Push Notifications won't work")
  }

  ```

- Then, click `continue to console`.

### 3.3 Configure Firebase Project

1. Generate **SHA 256 key** with the help of this command in Android Studio.(**Add correct path to locate the debug.keystore**)

```sh
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

2. Go to [Firebase Console](https://console.firebase.google.com/)
3. Navigate to **Project Settings**
4. Select **"General"** tab
5. In **your app** select **Add fingerprint** and paste the above generated **SHA 256 Key**.
6. Then navigate to **Cloud Messaging** tab.
7. In **Web configuration** click **Generate a key pair**.

Refer `useLogin.ts`, `PushNotificationListeners.tsx` and `LocalNotificationListeners.tsx` in **Palm Meadows** project for code implementation.

# 4. Listeners

Refer `MobileAppProvider.tsx`, `AuthLayout.tsx` and `MainLayout.tsx` in **Palm Meadows** project for Wrapping the Listeners. Find the listener components in **src ==> packages ==> core ==> capacitor-app ==> Listeners**.

# 5. Edge-To-Edge-Enforcement on API-35

For apps that target **Android 15**, we're including several changes related to edge-to-edge. Your app's user experience might be negatively impacted by these changes if your app has UI elements near the top or bottom of the screen and your app doesn't handle insets. To disable this feature add the following code in the `MainActivity.java` file.

**Before Modification**

```java
//MainActivity.java
package com.thingzmate.palm;

import android.os.Bundle;
import com.getcapacitor.BridgeActivity;

public class MainActivity extends BridgeActivity {}

```

**After Modification**

```java
package com.thingzmate.palm;

import android.os.Bundle;
import androidx.core.view.ViewCompat;   // ✅ Newly added code
import androidx.core.view.WindowInsetsCompat;  // ✅ Newly added code
import android.view.ViewGroup.MarginLayoutParams;  // ✅ Newly added code
import androidx.core.graphics.Insets;  // ✅ Newly added code
import com.getcapacitor.BridgeActivity;
import android.view.View;  // ✅ Newly added code

public class MainActivity extends BridgeActivity {
    // ✅ New block of code
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Get the root view
        View contentView = findViewById(android.R.id.content);

        // Apply window insets
        ViewCompat.setOnApplyWindowInsetsListener(contentView, (v, windowInsets) -> {
            Insets insets = windowInsets.getInsets(WindowInsetsCompat.Type.systemBars());
            MarginLayoutParams mlp = (MarginLayoutParams) v.getLayoutParams();
            mlp.leftMargin = insets.left;
            mlp.bottomMargin = insets.bottom;
            mlp.rightMargin = insets.right;
            mlp.topMargin = insets.top;
            v.setLayoutParams(mlp);
            return WindowInsetsCompat.CONSUMED;
        });
    }
    // ✅ New block of code
}
```
