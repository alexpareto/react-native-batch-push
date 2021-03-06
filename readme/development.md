# Install plugin for development

## 1. Setup a new Project

- Create a new app using `react-native init BatchTest`
- Go into the folder `cd BatchTest`
- Initialize the git repo and commit everything

## 2. Install the React Native Batch plugin

- Create a `mkdir local-modules`
- Go into the folder `cd local-modules`
- Create a `mkdir @bam.tech`
- Go into the folder `cd @bam.tech`
- Add `local-modules` to your _.gitignore_
- Clone the react-native-batch-push repository `git clone git@github.com:bamlab/react-native-batch-push.git`
- Rename `react-native-batch-push` to `react-native-batch`
- Checkout the required branch or a new branch
- Run `yarn` to install dependencies

## 3. Run build for development

- Open `local-modules/@bam.tech/react-native-batch` within VSCode
- Run the `Task run build task >> tsc: watch`

## 4. Install the plugin on Android

### a. Link the library

```groovy
// android/settings.gradle

include ':@bam.tech_react-native-batch'
project(':@bam.tech_react-native-batch').projectDir = new File(rootProject.projectDir, '../local-modules/@bam.tech/react-native-batch/android')
```

```groovy
// android/app/build.gradle

dependencies {
    implementation project(':@bam.tech_react-native-batch')
    ...
}
```

```java
// android/app/src/main/java/com/<AppName>/MainApplication.java

import tech.bam.RNBatchPush.RNBatchPackage;

public class MainApplication extends Application implements ReactApplication {
  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
          new RNBatchPackage()
      );
    }
  }
}
```

### b. Install Batch dependencies

```groovy
// android/build.gradle

buildscript {
    ...
    dependencies {
        ...
        classpath 'com.google.gms:google-services:4.2.0'
    }
}
```

```groovy
// android/app/build.gradle

dependencies {
    implementation "com.google.firebase:firebase-core:16.0.7"
    implementation "com.google.firebase:firebase-messaging:17.3.4"
    ...
}

apply plugin: 'com.google.gms.google-services'
```

### c. Add your Batch key

```groovy
// android/app/build.gradle

defaultConfig {
    ...
    resValue "string", "BATCH_API_KEY", "%YOUR_BATCH_API_KEY%"
}
```

### d. Add your Firebase config

- Add the _google-services.json_ file to `/android/app`

### d. Run the App

- Open the `/android` folder with _Android studio_
- Connect your phone to your computer
- Run `adb reverse tcp:8081 tcp:8081`
- Run `yarn start`
- Run the project in debug mode

## 5. Install the plugin on iOS

### a. Setup the Batch dependencies

- Go to `/ios`
- Run `pod init`
- Add `pod 'Batch', '~>1.13'` to your _Podfile_
- Run `pod install`

### b. Link the plugin

- Open `/ios/<ProjectName>.xcworkspace`
- Select _<ProjectName>_ in XCode
- Right click on _Librairies_ > _Add files to <Project Name>_
- Select `/local-modules/@bam.tech/react-native-batch/ios/RNBatchPush.xcodeproj`
- In the project window select
  - _Build Phases_
  - _Link Binary With Librairies_
  - _+_ > `libRNBatchPush.a`

### c. Enable Push Capabilities

- In the project window
- Go to _Capabilities_
- Toggle _Push Notifications_

### d. Configure your Batch key

Go to the Batch dashboard, create an iOS app and upload your iOS push certificate.

Then, in `Info.plist`, provide:

```xml
<key>BatchAPIKey</key>
<string>%YOUR_BATCH_API_KEY%</string>
```

### e. Run the App

- Run the project **on a real device** from XCode

## 6. Use the plugin in your app

```typescript
// App.js

import { Batch, BatchPush } from '@bam.tech/react-native-batch';

Batch.start();
// On iOS, to display the push authorization modal
BatchPush.registerForRemoteNotifications();
```

## 7. Extra

### a. Using the type definition with a JS project

```json
// jsconfig.json

{
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "@bam.tech/react-native-batch": [
        "./local-modules/@bam.tech/react-native-batch"
      ]
    }
  },
  "exclude": ["node_modules"]
}
```

### b. Check that the setup was succesful

- Add a Custom User Identifier

```typescript
import { BatchUser } from '@bam.tech/react-native-batch';

BatchUser.editor()
  .setIdentifier('<custom-id>')
  .save();
```

- Go to your project on Batch.com
- Go to the debug view
- Select _CUSTOM USER ID_ and write in your _<custom_id>_
- Press debug to retrieve your information
