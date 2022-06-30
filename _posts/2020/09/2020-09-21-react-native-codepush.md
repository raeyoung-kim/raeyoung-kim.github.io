---
title: react-native-codepush 배포하기
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-09-21
categories: [React Native]
tags: [react-native, react-native-codepush]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

앱 업데이트를 할 때 각 스토어의 심사를 받아야 하기 때문에 최소 하루 이상 시간이 소요된다. 
Microsoft에서 서비스하고 있는 Code Push를 사용하면 스토어의 심사를 받지 않고 빠르게 앱을 업데이트할 수 있다.
하지만 일부 기능이 추가되어 빌드를 해야 하는 경우를 제외한다.
간단하게 javascript 나 스타일같이 간단한 수정을 했을 때 바로 적용할 수 있다.

## setUp
---

```bash
sudo npm install -g code-push-cli  //code-push-cli을 설치  
npm install -g appcenter-cli 
npm install --save react-native-code-push
npx react-native link react-native-code-push
```

- codepush에 앱을 등록하기 위해 아래 명령어를 실행하고 Visual Studio App Center에 로그인한다.

```bash
    code-push register
```

- 로그인 후 발급된 인증 토큰을 복사한 뒤 터미널에 붙여넣기한다.
- Android와 iOS 서비스를 등록한다.

```bash
    code-push app add <myAppName-android> android react-native
    code-push app add <myAppName-ios> ios react-native
```

- 서비스 등록이 완료되면 Deployment Production Key 와 Staging Key 확인할 수 있다. 
Deployment Key 확인하려면 아래 명령어를 실행한다.

```bash
    code-push deployment ls <myAppName-android> -k
    code-push deployment ls <myAppName-ios> -k
```

- 등록한 서비스 확인 명령어

    ```bash
    code-push app list
    ```

## ios 설정
---

- [ios setUp](https://github.com/microsoft/react-native-code-push/blob/master/docs/setup-ios.md)

- Add App Center install

```bash
    npm install appcenter appcenter-analytics appcenter-crashes --save-exact
```

- `ios/AppCenter-Comfig.plist` 파일에 아래 소스를 추가한다.

```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
        <dict>
        <key>AppSecret</key>
        <string>--appcenter key--</string>
        </dict>
    </plist>
```

- `/ios/<app-name>/AppDelegate.m` 파일에 아래 소스를 추가한다.

```csharp
    #import <CodePush/CodePush.h>
    #import <AppCenterReactNative.h>
    #import <AppCenterReactNativeAnalytics.h>
    #import <AppCenterReactNativeCrashes.h>

    - (NSURL *)sourceURLForBridge:(RCTBridge *)bridge
    {
      #if DEBUG
        return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index" fallbackResource:nil];
      #else
        return [CodePush bundleURL];
      #endif
    }
```

- `/ios/<app-name>/info.plist`
CodePushDeploymentKey에 CodePush에서 받은 Staging Deployment Key을 입력한다.

```xml
    <key>CodePushDeploymentKey</key>
    <string>--Staging Deployment Key--</string>
```

- `cd ios && pod install && cd ..`

## android 설정

---
- [안드로이드 setUp](https://github.com/microsoft/react-native-code-push/blob/master/docs/setup-android.md)

- Add App Center install.

```bash
    npm install appcenter appcenter-analytics appcenter-crashes --save-exact
```

- `android/app/src/main/assets/appcenter-config.json`

```json
    {
      "app_secret": "<app_secret_value>"
    }
```

- `android/settings.gradle` 파일에 아래 소스를 추가한다.

    ```gradle
    include ':app', ':react-native-code-push'
    project(':react-native-code-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-code-push/android/app')
    ```

- `android/app/build.gradle` 파일 가장 아래에 아래 소스를 추가한다.
(Cannot add task ‘bundleDebugJsAndAssets’ as a task with that name already exists. 에러가 발생하면 이미 같은 작업의 이름이 있다는 것. 파일을 확인한 후 중복되어 있는  (예: apply from: “../../node_modules/react-native/react.gradle”) 부분을 제거한다.

```gradle
    apply from: "../../node_modules/react-native/react.gradle"
    apply from: "../../node_modules/react-native-code-push/android/codepush.gradle"
```

- `android/app/src/main/java/<app-name>/MainApplication.java` 
ReactNativeHost 설정에 getJSBundleFile() 추가한다.

```java
    import com.microsoft.codepush.react.CodePush;

    @Override
    protected String getJSBundleFile() {
        return CodePush.getJSBundleFile();
    }
```

- `android/app/src/main/res/values/strings.xml` 파일에 CodePushDeploymentKey를 추가한다.

```xml
    <resources>
        <string name="app_name">fetching-android</string>
        <string moduleConfig="true" name="CodePushDeploymentKey">--Staging Deployment Key--</string>
    </resources>
```

- [multi-deployment-testing-android](https://github.com/Fetching-LTD/react-native-code-push/blob/master/docs/multi-deployment-testing-android.md)
- `android/app/build.gradle` 에 아래 소스를 추가하고 `android/app/src/main/res/values/strings.xml` 파일에 CodePushDeploymentKey를 삭제한다.

```gradle
    android {
        ...
        buildTypes {
            debug {
                ...
                // Note: CodePush updates should not be tested in Debug mode as they are overriden by the RN packager. However, because CodePush checks for updates in all modes, we must supply a key.
                resValue "string", "CodePushDeploymentKey", '""'
                ...
            }

            releaseStaging {
                ...
                resValue "string", "CodePushDeploymentKey", '"<INSERT_STAGING_KEY>"'

                // Note: It is a good idea to provide matchingFallbacks for the new buildType you create to prevent build issues
                // Add the following line if not already there
                matchingFallbacks = ['release']
                ...
            }

            release {
                ...
                resValue "string", "CodePushDeploymentKey", '"<INSERT_PRODUCTION_KEY>"'
                ...
            }
        }
        ...
    }
```

## code-push 업데이트
---

- [앱 센터](https://appcenter.ms/apps)

```bash
code-push release-react <appName> <platform> -d Staging [or Production]
```

- android

```bash
    appcenter codepush release-react -a <project.co.kr>/<projectname-android> -d Staging [or Production] 
```

- ios

```bash
    appcenter codepush release-react -a <project.co.kr>/<projectname-ios> -d Staging [or Production] 
```

- Production으로 배포할 경우 실 배포 앱에 적용된다.
---

![](https://images.velog.io/images/760kry/post/54c7c70c-78bc-4d42-9e93-b5a75aa65aac/Screen%20Shot%202020-09-20%20at%2010.47.32%20PM.png)
![](https://images.velog.io/images/760kry/post/143360c1-a6c5-4033-9322-67e7e2c44187/Screen%20Shot%202020-09-20%20at%2011.23.32%20PM.png)

이렇게 배포가 성공했는데 실 배포 앱에 적용되지 않는다면 배포된 파일의 appcenter key 와 staging key와 현재 적용하고 있는 파일의 appcenter key 와 staging key가 일치하는지 확인해 봐야 한다.
