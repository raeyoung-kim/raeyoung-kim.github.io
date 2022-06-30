---
title: react-native-webview에서 외부 앱 실행 (pg)
author:
  name: kim rae young
  link: https://github.com/raeyoung-kim
date: 2020-10-20
categories: [React Native]
tags: [react-native-webview 외부 앱 실행(pg)]
math: true
mermaid: true
# image:
#   src: /commons/devices-mockup.png
#   width: 800
#   height: 500
---

react-native-webview를 이용하여 모바일 web으로 이동한 후 앱 카드 결제 시 앱 카드 앱 호출이 되지 않는 이슈가 발생하였다. 구글링을 통해 마침내 외부 앱을 실행했다.. 정확한 원리와 이해를 완벽히 한 건 아니지만 내가 시도했던 것들을 아래에 정리해 보았다.

## android
---

안드로이드의 경우 intent 호출을 할 수 없기 때문에 라이브러리를 설치해 intent 호출을 할 수 있었다.

### install

- [react-native-send-intent](https://www.npmjs.com/package/react-native-send-intent)

```bash
npm install react-native-send-intent --save

npx react-native link react-native-send-intent
```

- `android/setting.gradle`

```java
    ...
    include ':RNSendIntentModule', ':app'
    project(':RNSendIntentModule').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-send-intent/android')
```

- `android/app/build.gradle`

```java
    ...
    dependencies {
        ...
        compile project(':RNSendIntentModule')
    }
```
  
  
구글링을 통해 [here1]( https://reactnativeseoul.org/t/app/494/3) 를 발견했다.
댓글에 나와있던 [here2](https://github.com/harudev/react-native-send-intent/blob/7b17e6580147a6e88cf8f05efc98a7fc9720a904/android/src/main/java/com/burnweb/rnsendintent/RNSendIntentModule.java#L582-L600) 와 같이 아래 파일을 수정해서 openAppWithUri함수를 만들었다.

- `node_modules/react-native-send-intent/android/src/main/java/com/burnweb/rnsendintent/RNSendIntentModule.java` 에 아래 소수를 추가한다.

```java
    ...
    @ReactMethod
        public void openAppWithUri(String intentUri, ReadableMap extras, final Promise promise) {
            try {
                Intent intent = Intent.parseUri(intentUri, Intent.URI_INTENT_SCHEME);
                intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                Intent existPackage = this.reactContext.getPackageManager().getLaunchIntentForPackage(intent.getPackage());
                if (existPackage != null) {
                    this.reactContext.startActivity(intent);
                } else {
                    Intent marketIntent = new Intent(Intent.ACTION_VIEW);
                    marketIntent.setData(Uri.parse("market://details?id="+intent.getPackage()));
                    marketIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                    this.reactContext.startActivity(marketIntent);
                }
                promise.resolve(true);
            } catch (Exception e) {
                promise.resolve(false);
            }
        }
    ...
```

- `node_modules/react-native-send-intent/index.js` 에 아래 소스를 추가한다.

```jsx
    openAppWithUri(intentUri, extras) {
            return RNSendIntentAndroid.openAppWithUri(intentUri, extras || {});
    }
```

  ## ios
  ---

  - `AppDelegate.m` 파일에 아래의 소스를 추가한다.
    - [참고 문서](https://doc.ebichu.cc/react-native/releases/0.26/docs/linking.html)

```
    - (BOOL)application:(UIApplication *)application
       openURL:(NSURL *)url
       options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
      return [RCTLinkingManager application:application openURL:url options:options];
    }

    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url
      sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
    {
      return [RCTLinkingManager application:application openURL:url
                          sourceApplication:sourceApplication annotation:annotation];
    }

    - (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity
     restorationHandler:(void (^)(NSArray * _Nullable))restorationHandler
    {
     return [RCTLinkingManager application:application
                      continueUserActivity:userActivity
                        restorationHandler:restorationHandler];
    }
```
  
  iOS 9 버전부터 LSApplicationQueriesSchemes라는 Info 항목에 URL scheme을 사용해서 외부 앱을 열 경우, 특별한 제한이 없던 기존 방식에서 화이트리스트에 등록된 scheme만 열 수 있도록 보안 정책이 강화되어 화이트리스트에 등록되지 않은 경우, 웹뷰에서는 무조건 차단하는 정책으로 변경되었기 때문에 `info.plist` 파일에  `LSApplicationQueriesSchemes`에 스키마를 추가한다.

```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>lguthepay://</string>
    	<string>lguthepay-xpay://</string>
    	<string>smartxpay-transfer://</string>
    	<string>nhappcash-acp:// </string>
    	<string>SmartBank2WB://</string>
    	<string>hdcardappcardansimclick://</string>
    	<string>smhyundaiansimclick://</string>
    	<string>shinhan-sr-ansimclick://</string>
    	<string>smshinhanansimclick://</string>
    	<string>kb-acp://</string>
    	<string>mpocket.online.ansimclick://</string>
    	<string>ansimclickscard://</string>
    	<string>tswansimclick://</string>
    	<string>ansimclickipcollect://</string>
    	<string>vguardstart://</string>
    	<string>samsungpay</string>
    	<string>scardcertiapp://</string>
    	<string>lottesmartpay://</string>
    	<string>lpayapp://</string>
    	<string>lotteappcard://</string>
    	<string>payco://</string>
    	<string>cloudpay://</string>
    	<string>hanamopmoasign://</string>
    	<string>hanawalletmembers://</string>
    	<string>nhappcardansimclick://</string>
    	<string>nhallonepayansimclick://</string>
    	<string>nonghyupcardansimclick://</string>
    	<string>citispay://</string>
    	<string>citicardappkr://</string>
    	<string>ispmobile://</string>
    	<string>uppay://</string>
    	<string>shinsegaeeasypayment://</string>
    	<string>wooripay://</string>
    	<string>kftc-bankpay</string>
    	<string>itms-apps</string>
    	<string>citimobileapp</string>
    	<string>kakaotalk</string>
    </array>
    <key>NSAppTransportSecurity</key>
    <dict>
    	<key>NSAllowsArbitraryLoads</key>
    	<true/>
    	<key>NSExceptionDomains</key>
    	<dict>
    	   <key>localhost</key>
    	   <dict>
    	      <key>NSExceptionAllowsInsecureHTTPLoads</key>
    	      <true/>
    	   </dict>
    	</dict>
    </dict>
```

## react-native-webview
---

RN 웹뷰 내에서 onShouldStartLoadWithRequest 메소드를 이용해서 url이 바뀔 때마다 감지해서 http, https 외의 외부 앱 호출일 경우 android 경우 react-native-send-intent 등 외부 라이브러리를 이용해 intent를 호출하고 ios의 경우 Linking.openUrl을 사용해 intent를 호출한다.

- [originWhitelist 참고문서](https://reactnative.dev/docs/webview#originwhitelist)
- [onShouldStartLoadWithRequest 참고 문서](https://reactnative.dev/docs/webview#onshouldstartloadwithrequest)
- `originWhitelist={['*']}` 를 넣고 `onShouldStartLoadWithRequest` 로 핸들링한다.

```jsx
    import {WebView} from 'react-native-webview';
    import {Linking, Platform} from 'react-native';

    ...

    const onShouldStartLoadWithRequest = () => {
      if (
          event.url.startsWith('http://') ||
          event.url.startsWith('https://') ||
          event.url.startsWith('about:blank')
        ) {
          return true;
        }
        if (Platform.OS === 'android') {
          SendIntentAndroid.openAppWithUri(event.url)
            .then(isOpened => {
              if (!isOpened) {
                alert('앱 실행에 실패했습니다');
              }
            })
            .catch(err => {
              console.log(err);
            });
        } else {
          Linking.openURL(event.url).catch(err => {
            alert(
              '앱 실행에 실패했습니다. 설치가 되어있지 않은 경우 설치하기 버튼을 눌러주세요.',
            );
          });
          return false;
        }
    };
    ...

    <WebView
       ref={webview}
       originWhitelist={['*']}
       source={/* url ... */}
       onLoadStart={() => onLoadStart()}
       onLoadProgress={({nativeEvent}) => onLoadProgress(nativeEvent)}
       onLoad={() => onLoadEnd()}
       onNavigationStateChange={navState => onChangeNavState(navState)}
       onShouldStartLoadWithRequest={event => {
         return onShouldStartLoadWithRequest(event);
       }}
       onMessage={event => onMessage(event)}
    />
```
