---
title: Android 앱에서 인증 사용 - Azure AD B2C
description: Azure Active Directory B2C 구성 요소를 사용하여 Android 애플리케이션에서 인증을 사용하도록 설정 다음으로, Azure AD B2C를 사용하여 Android 애플리케이션에 사용자를 로그인하고 등록하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/06/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support, has-adal-ref
ms.openlocfilehash: b9341909f6dd1dd9d01408a7b7af46e47a0ff339
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539114"
---
# <a name="enable-authentication-in-your-own-android-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 자체 Android 애플리케이션에서 인증을 활성화합니다.

이 문서에서는 자체 Android 모바일 애플리케이션에 Azure Active Directory B2C(Azure AD B2C) 인증을 추가하는 방법을 보여줍니다. 

[샘플 Android 애플리케이션에서 인증 구성](./configure-authentication-sample-android-app.md)함께 이 문서를 사용하여 샘플 Android 앱을 사용자 고유의 Android 앱으로 바꿉니다. 이 문서의 단계를 완료하면 애플리케이션이 Azure AD B2C 통해 로그인을 수락합니다.

## <a name="prerequisites"></a>필수 구성 요소

[샘플 Android 애플리케이션에서 인증 구성](configure-authentication-sample-android-app.md)의 필수 조건 및 통합 단계를 검토합니다.

## <a name="create-an-android-app-project"></a>Android 앱 프로젝트를 만듭니다.

아직 Android 애플리케이션이 없는 경우 다음 단계에 따라 새 프로젝트를 설정합니다.

1. Android Studio를 열고 **새 Android Studio 프로젝트 시작** 을 선택합니다.
2. **기본 작업** 을 선택하고 **다음** 을 선택합니다.
3. 애플리케이션의 이름을 지정합니다.
4. 패키지 이름을 저장합니다. 나중에 Azure Portal에 입력해야 합니다.
5. 언어를 **Kotlin** 에서 **Java** 로 변경합니다.
6. **최소 API 레벨** 을 **API 19** 또는 그 이상으로 설정하고 **마침** 을 클릭합니다.
7. 프로젝트 보기의 드롭다운 목록에서 **프로젝트** 를 선택하여 소스 및 소스가 아닌 프로젝트 파일을 표시하고, **app/build.gradle** 을 열고, `targetSdkVersion`을 `28`로 설정합니다.

## <a name="install-the-dependencies"></a>종속성 설치

Android Studio 프로젝트 창에서 **app** > **build.gradle** 로 이동하여 다음을 추가합니다.

```gradle
apply plugin: 'com.android.application'

allprojects {
    repositories {
    mavenCentral()
    google()
    mavenLocal()
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    maven {
        name "vsts-maven-adal-android"
        url "https://identitydivision.pkgs.visualstudio.com/_packaging/AndroidADAL/maven/v1"
        credentials {
            username System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_USERNAME") : project.findProperty("vstsUsername")
            password System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") != null ? System.getenv("ENV_VSTS_MVN_ANDROIDADAL_ACCESSTOKEN") : project.findProperty("vstsMavenAccessToken")
        }
    }
    jcenter()
    }
}
dependencies{
    implementation 'com.microsoft.identity.client:msal:2.+'
    }
packagingOptions{
    exclude("META-INF/jersey-module-version")
}
```


## <a name="add-the-authentication-components"></a>인증 구성 요소 추가

[샘플 코드](configure-authentication-sample-android-app.md#step-3-get-the-android-mobile-app-sample)는 다음 구성 요소로 구성됩니다. 샘플 Android 앱에서 사용자 고유의 앱에 이러한 구성 요소를 추가합니다. 

|구성 요소  |형식  | 원본 |설명  |
|---------|---------|---------|---------|
| B2CUser| 클래스| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CUser.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CUser.java)| B2C 사용자를 나타냅니다. 이 클래스를 사용하면 사용자가 여러 정책을 사용하여 로그인할 수 있습니다. | 
| B2CModeFragment | 조각 클래스| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CModeFragment.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CModeFragment.java)| 조각은 기본 활동 내에서 Azure AD B2C 사용자 인터페이스를 통해 로그인의 모듈식 부분을 나타냅니다. 이 조각에는 대부분의 인증 코드가 포함되어 있습니다. |
| fragment_b2c_mode.xml | 조각 레이아웃| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/layout/fragment_b2c_mode.xml) | B2CModeFragment 조각 구성 요소에 대한 사용자 인터페이스의 구조를 정의합니다. |
| B2CConfiguration| 클래스| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/java/com/azuresamples/msalandroidkotlinapp/B2CConfiguration.kt) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/java/com/azuresamples/msalandroidapp/B2CConfiguration.java)| 구성 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 모바일 앱은 이 정보를 사용하여 Azure AD B2C와 트러스트 관계를 설정하고, 사용자를 로그인 및 로그아웃 시키고, 토큰을 획득하고, 유효성을 검사합니다. 자세한 구성 설정은 auth_config_b2c.js 파일을 참조하세요.  | 
|auth_config_b2c.json | JSON 파일| [Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/blob/master/app/src/main/res/raw/auth_config_b2c.json) [Java](https://github.com/Azure-Samples/ms-identity-android-java/blob/master/app/src/main/res/raw/auth_config_b2c.json)| 구성 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 모바일 앱은 이 정보를 사용하여 Azure AD B2C와 트러스트 관계를 설정하고, 사용자를 로그인 및 로그아웃 시키고, 토큰을 획득하고, 유효성을 검사합니다. 자세한 구성 설정은 B2CConfiguration 클래스를 참조하세요. | 

## <a name="configure-your-android-app"></a>Android 앱 구성

[인증 구성 요소를 추가](#add-the-authentication-components)한 후 Azure AD B2C 설정을 통해 Android 앱을 구성합니다. Azure AD B2C ID 공급자 설정은 파일 및 B2CConfiguration 클래스와 auth_config_b2c.json file 파일로 구성됩니다. 

[샘플 모바일 앱을 구성하는](configure-authentication-sample-android-app.md#step-5-configure-the-sample-mobile-app) 방법에 대한 지침을 따르세요.

## <a name="set-the-redirect-uri"></a>리디렉션 URI 설정합니다.

이 섹션에서는 애플리케이션이 Azure AD B2C 토큰 응답을 수신 대기하는 위치를 구성합니다. 


1. 새 개발 시그니쳐 해시 코드를 생성합니다. 이는 각 개발 환경마다 변경됩니다.
    
    Windows 운영 체제:
    
    ```
    keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    IOS 운영 체제:
    
    ```dotnetcli
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ```
    
    프로덕션 환경의 경우 다음 명령을 사용합니다.
    
    ```
    keytool -exportcert -alias SIGNATURE_ALIAS -keystore PATH_TO_KEYSTORE | openssl sha1 -binary | openssl base64
    ```

    앱 서명 체크 아웃에 대한 자세한 도움말은 [Android 앱 서명](https://developer.android.com/studio/publish/app-signing)을 확인하세요. 

1. **app** > **src** > **main** > **AndroidManifest.xml** 에서 `BrowserTabActivity`아래의 작업을 애플리케이션 본문에 추가합니다.
    
    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Package_Name"
                android:path="/Signature_Hash" />
        </intent-filter>
    </activity>
    ```
1. `Signature_Hash`을 생성한 해시 코드로 대체합니다.
1. `Package_Name`을 Android 패키지 이름으로 대체합니다.
 
앱 리디렉션 URI를 사용하여 모바일 앱 등록을 업데이트하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 후, [2.3 모바일 앱 등록](configure-authentication-sample-android-app.md#23-register-the-mobile-app)에서 등록한 애플리케이션을 선택합니다.
1. **인증** 을 선택합니다.
1. **Android** 아래에서 **URI 추가** 를 선택합니다.
1. **패키지 이름** 과 **시그니쳐 해시 코드** 를 입력합니다.
1. **저장** 을 선택합니다.

리디렉션 URI 및 `BrowserTabActivity`활동은 다음 샘플과 유사해야 합니다. 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

샘플 Android에 대한 리디렉션 URL:

```
msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

그런 다음 의도 필터는 다음 XML 조각과 동일한 패턴을 사용합니다.

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data
            android:host="com.azuresamples.msalandroidkotlinapp"
            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
            android:scheme="msauth" />
    </intent-filter>
</activity>
```



#### <a name="java"></a>[Java](#tab/java)

샘플 Android에 대한 리디렉션 URL:

```
msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D
```

그런 다음 의도 필터는 다음 XML 조각과 동일한 패턴을 사용합니다.

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
<intent-filter>
    <action android:name="android.intent.action.VIEW" />

    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />

    <data
        android:host="com.azuresamples.msalandroidapp"
        android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
        android:scheme="msauth" />
</intent-filter>
</activity>
```

--- 

## <a name="code-building-blocks"></a>코드 구성 요소

이 섹션에서는 Android 앱에 대한 인증을 사용하도록 설정하는 코드 구성 요소에 대해 설명합니다. 다음 표에서는 B2CModeFragment의 메서드와 코드를 사용자 지정하는 방법을 나열합니다. 

### <a name="instantiate-a-public-client-application"></a>퍼블릭 클라이언트 애플리케이션 인스턴스화

퍼블릭 클라이언트 애플리케이션은 애플리케이션 비밀을 안전하게 유지하는 데 신뢰할 수 없으며 클라이언트 암호가 없습니다. [onCreate](https://developer.android.com/reference/android/app/Fragment#onCreate(android.os.Bundle)) 또는 [onCreateView](https://developer.android.com/reference/android/app/Fragment#onCreateView(android.view.LayoutInflater,%20android.view.ViewGroup,%20android.os.Bundle))에서 여러 계정 공용 클라이언트 애플리케이션 개체를 사용하여 MSAL을 인스턴스화합니다.

`MultipleAccountPublicClientApplication` 클래스는 여러 계정이 동시에 로그인할 수 있는 MSAL 기반 앱을 만드는 데 사용됩니다. 여러 Azure AD B2C 사용자 흐름 또는 사용자 지정 정책을 통해 로그인할 수 있습니다. 예를 들어 사용자가 [등록 또는 로그인](add-sign-up-and-sign-in-policy.md) 사용자 흐름을 통해 로그인하고 나중에 [프로필 편집](add-profile-editing-policy.md) 사용자 흐름을 실행합니다. 

다음 코드 조각에서는 `auth_config_b2c.json`구성 JSON 파일을 사용하여 MSAL 라이브러리를 시작하는 방법을 보여줍니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
PublicClientApplication.createMultipleAccountPublicClientApplication(context!!,
    R.raw.auth_config_b2c,
    object : IMultipleAccountApplicationCreatedListener {
        override fun onCreated(application: IMultipleAccountPublicClientApplication) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application
            // Load the account (if there is any)
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            // Error handling
            displayError(exception)
        }
    })
```

#### <a name="java"></a>[Java](#tab/java)

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
    R.raw.auth_config_b2c,
    new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication application) {
            // Set the MultipleAccountPublicClientApplication to the class member b2cApp
            b2cApp = application;

            // Load the account (if there is any)
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            // Error handling
            displayError(exception);
        }
    });
```

--- 

### <a name="load-accounts"></a>계정 로드

앱이 전경에 오면 앱은 기존 계정을 로드하여 사용자가 로그인했는지 여부를 확인합니다.  이 방법을 사용하여 인증 상태로 UI를 업데이트합니다. 예를 들자면 로그아웃 단추 사용 여부를 설정합니다.

다음 코드 조각은 계정을 로드를 수행하는 방법을 보여줍니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private fun loadAccounts() {
    if (b2cApp == null) {
        return
    }
    b2cApp!!.getAccounts(object : LoadAccountsCallback {
        override fun onTaskCompleted(result: List<IAccount>) {
            users = B2CUser.getB2CUsersFromAccountList(result)
            updateUI(users)
        }
    
        override fun onError(exception: MsalException) {
            displayError(exception)
        }
    })
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private void loadAccounts() {
    if (b2cApp == null) {
        return;
    }

    b2cApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            users = B2CUser.getB2CUsersFromAccountList(result);
            updateUI(users);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

--- 

### <a name="interactive-authorization-request"></a>대화형 권한 부여 요청

대화형 권한 부여 요청은 사용자에게 등록 또는 로그인하라는 메시지가 표시되는 흐름입니다. `initializeUI`메서드는 클릭 이벤트를 구성합니다`runUserFlowButton`. 사용자가 **사용자 흐름 실행** 단추를 선택하면 앱에서 사용자를 Azure AD B2C 로그인 흐름을 완료합니다. 

`runUserFlowButton.setOnClickListener`메서드는 권한 부여 요청에 대한 관련 데이터를 사용하여 `AcquireTokenParameters`개체를 준비합니다. 그런 다음, `acquireToken`메서드는 사용자에게 등록 또는 로그인 흐름을 완료하라는 메시지를 표시합니다. 

다음 코드 조각은 대화형 권한 부여 요청을 시작하는 방법을 보여줍니다. 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
val parameters = AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(activity)
        .fromAuthority(getAuthorityFromPolicyName(policy_list.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.scopes)
        .withPrompt(Prompt.LOGIN)
        .withCallback(authInteractiveCallback)
        .build()

b2cApp!!.acquireToken(parameters)
```

#### <a name="java"></a>[Java](#tab/java)

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
        .startAuthorizationFromActivity(getActivity())
        .fromAuthority(B2CConfiguration.getAuthorityFromPolicyName(policyListSpinner.getSelectedItem().toString()))
        .withScopes(B2CConfiguration.getScopes())
        .withPrompt(Prompt.LOGIN)
        .withCallback(getAuthInteractiveCallback())
        .build();

b2cApp.acquireToken(parameters);
```

--- 

 
### <a name="interactive-authorization-request-callback"></a>대화형 권한 부여 요청 콜백

사용자가 권한 부여 흐름을 완료하면(성공 또는 실패) 결과가 `getAuthInteractiveCallback()`콜백 메서드로 반환됩니다. 

콜백 메서드는`AuthenticationResult`개체 또는 `MsalException`개체의 오류 메시지를 전달합니다. 이 메서드를 사용하여 다음을 수행할 수 있습니다.

- 모바일 앱 UI를 인증이 완료된 후의 정보로 업데이트합니다.
- 계정 개체 다시 로드
- 액세스 토큰을 사용하여 웹 API 서비스를 호출합니다.
- 인증 오류를 처리합니다.

다음 코드 조각은 대화형 인증 콜백을 사용하는 것을 보여줍니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```kotlin
private val authInteractiveCallback: AuthenticationCallback
    private get() = object : AuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            /* Successfully got a token, use it to call a protected resource; web API  */
            Log.d(TAG, "Successfully authenticated")

            /* display result info */
            displayResult(authenticationResult)

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts()
        }

        override fun onError(exception: MsalException) {
            val B2C_PASSWORD_CHANGE = "AADB2C90118"
            if (exception.message!!.contains(B2C_PASSWORD_CHANGE)) {
                txt_log!!.text = """
                    The user clicks the 'Forgot Password' link in a sign-up or sign-in user flow.
                    Your application needs to handle this error code by running a specific user flow that resets the password.
                    """.trimIndent()
                return
            }

            /* Failed to acquireToken */Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        override fun onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.")
        }
    }
```

#### <a name="java"></a>[Java](#tab/java)

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");

            /* display result info */
            displayResult(authenticationResult);

            /* Reload account asynchronously to get the up-to-date list. */
            loadAccounts();
        }

        @Override
        public void onError(MsalException exception) {
            final String B2C_PASSWORD_CHANGE = "AADB2C90118";
            if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
                logTextView.setText("The user clicks the 'Forgot Password' link in a sign-up or sign-in user flow.\n" +
                        "Your application needs to handle this error code by running a specific user flow that resets the password.");
                return;
            }

            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

--- 

## <a name="call-a-web-api"></a>웹 API 호출

[토큰 기반 권한 부여 웹 API](enable-authentication-web-api.md)를 호출하려면 앱에 유효한 액세스 토큰이 있어야 합니다. 앱은 다음 작업을 수행합니다.


1. 웹 API 엔드포인트에 필요한 권한(범위)을 사용하여 액세스 토큰을 획득합니다.
1. 다음 형식을 사용하여 액세스 토큰을 HTTP 요청의 인증 헤더에 전달자 토큰으로 전달합니다.

```http
Authorization: Bearer <access-token>
```

사용자가 대화형으로 [로그인하면](#interactive-authorization-request) 앱은 `getAuthInteractiveCallback`콜백 메서드에서 액세스 토큰을 가져옵니다. 연속 웹 API 호출의 경우 이 섹션에 설명된 대로 토큰 자동 획득 프로시저를 사용합니다. 

웹 API를 호출하기 전에 웹 API 엔드포인트에 대한 적절한 범위를 사용하여 `acquireTokenSilentAsync`메서드를 호출합니다. MSAL 라이브러리는 다음 단계를 수행합니다.

1. 토큰 캐시에서 요청된 범위가 있는 액세스 토큰을 가져오려고 시도합니다. 토큰이 있으면 토큰이 반환됩니다. 
1. 토큰이 토큰 캐시에 없는 경우 MSAL은 새로 고침 토큰을 사용하여 새 토큰을 획득하려고 시도합니다. 
1. 새로 고침 토큰이 없거나 만료된 경우 예외가 반환됩니다. 사용자에게 [대화형으로 로그인하라는](#interactive-authorization-request)메시지를 표시합니다.  

다음 코드 조각은 액세스 토큰을 획득하는 방법을 보여줍니다.

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)

`acquireTokenSilentButton`단추 클릭 이벤트는 제공된 범위가 있는 액세스 토큰을 획득합니다. 

```kotlin
btn_acquireTokenSilently.setOnClickListener(View.OnClickListener {
    if (b2cApp == null) {
        return@OnClickListener
    }
    val selectedUser = users!![user_list.getSelectedItemPosition()]
    selectedUser.acquireTokenSilentAsync(b2cApp!!,
            policy_list.getSelectedItem().toString(),
            B2CConfiguration.scopes,
            authSilentCallback)
})
```

`authSilentCallback` 콜백 메서드는 액세스 토큰을 반환하고 웹 API를 호출합니다.

```kotlin
private val authSilentCallback: SilentAuthenticationCallback
    private get() = object : SilentAuthenticationCallback {
        override fun onSuccess(authenticationResult: IAuthenticationResult) {
            Log.d(TAG, "Successfully authenticated")

            /* Call your web API here*/
            callWebAPI(authenticationResult)
        }

        override fun onError(exception: MsalException) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: $exception")
            displayError(exception)
            if (exception is MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception is MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception is MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    }
```

다음 예시에서는 전달자 토큰을 사용하여 보호된 웹 API를 호출하는 방법을 보여 있습니다.

```kotlin
@Throws(java.lang.Exception::class)
private fun callWebAPI(authenticationResult: IAuthenticationResult) {
    val accessToken = authenticationResult.accessToken
    val thread = Thread {
        try {
            val url = URL("https://your-app-service.azurewebsites.net/helo")
            val conn = url.openConnection() as HttpsURLConnection
            conn.setRequestProperty("Accept", "application/json")
            
            // Set the bearer token
            conn.setRequestProperty("Authorization", "Bearer $accessToken")
            if (conn.responseCode == HttpURLConnection.HTTP_OK) {
                val br = BufferedReader(InputStreamReader(conn.inputStream))
                var strCurrentLine: String?
                while (br.readLine().also { strCurrentLine = it } != null) {
                    Log.d(TAG, strCurrentLine)
                }
            }
            conn.disconnect()
        } catch (e: IOException) {
            e.printStackTrace()
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
    thread.start()
}
```

#### <a name="java"></a>[Java](#tab/java)

`acquireTokenSilentButton`단추 클릭 이벤트는 제공된 범위가 있는 액세스 토큰을 획득합니다. 

```java
acquireTokenSilentButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        if (b2cApp == null) {
            return;
        }

        final B2CUser selectedUser = users.get(b2cUserList.getSelectedItemPosition());
        selectedUser.acquireTokenSilentAsync(b2cApp,
                policyListSpinner.getSelectedItem().toString(),
                B2CConfiguration.getScopes(),
                getAuthSilentCallback());
    }
});
```

`authSilentCallback` 콜백 메서드는 액세스 토큰을 반환하고 웹 API를 호출합니다.

```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Call your web API here*/
            callWebAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

다음 예시에서는 전달자 토큰을 사용하여 보호된 웹 API를 호출하는 방법을 보여 있습니다.

```java
private void callWebAPI(IAuthenticationResult authenticationResult) throws Exception {
    final String accessToken = authenticationResult.getAccessToken();
    
    
    Thread thread = new Thread(new Runnable() {
    
        @Override
        public void run() {
            try {
                URL url = new URL("https://your-app-service.azurewebsites.net/helo");
                HttpsURLConnection conn = (HttpsURLConnection)url.openConnection();
                conn.setRequestProperty("Accept", "application/json");
                
                // Set the bearer token
                conn.setRequestProperty("Authorization", "Bearer " +  accessToken);
    
                if (conn.getResponseCode() == HttpURLConnection.HTTP_OK) {
                    BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
                    String strCurrentLine;
                    while ((strCurrentLine = br.readLine()) != null) {
                        Log.d(TAG, strCurrentLine);
                    }
                }
                conn.disconnect();
            } catch (IOException e) {
                e.printStackTrace();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    });
    
    thread.start();
    
    }
```

--- 

### <a name="add-permission-to-perform-network-operations"></a>네트워크 작업을 수행할 수 있는 권한 추가합니다.

애플리케이션에서 네트워크 작업을 수행하려면 매니페스트에 다음 권한을 포함합니다. 자세한 내용은 [네트워크에 연결](https://developer.android.com/training/basics/network-ops/connecting)을 참조하세요.

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

## <a name="next-steps"></a>다음 단계

* [Android 애플리케이션에서 인증 옵션 구성](enable-authentication-android-app-options.md)
* [웹 API에서 인증을 사용하도록 설정합니다.](enable-authentication-web-api.md)
