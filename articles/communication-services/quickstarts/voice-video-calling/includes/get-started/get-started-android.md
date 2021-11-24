---
title: 빠른 시작 - Azure Communication Services를 사용하여 Android 앱에 VOIP 통화 추가
description: 이 자습서에서는 Android용 Azure Communication Services Calling SDK를 사용하는 방법을 알아봅니다.
author: chpalm
ms.author: rifox
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: mode-other
ms.openlocfilehash: cfa6348e1a0906fd20bf4d15da24fcd3aaa1705c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133039297"
---
이 빠른 시작에서는 Android용 Azure Communication Services Calling SDK를 사용하여 통화를 시작하는 방법에 대해 알아봅니다.

## <a name="sample-code"></a>샘플 코드

샘플 앱은 [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android 스튜디오](https://developer.android.com/studio)(Android 애플리케이션 만들기용)
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- [사용자 액세스 토큰](../../../access-tokens.md)(Azure Communication Service용)

## <a name="setting-up"></a>설치

### <a name="create-an-android-app-with-an-empty-activity"></a>빈 활동을 사용하여 Android 앱 만들기

Android 스튜디오에서 [새 Android 스튜디오 프로젝트 시작]을 선택합니다.

:::image type="content" source="../../media/android/studio-new-project.png" alt-text="Android 스튜디오에서 선택한 '새 Android 스튜디오 프로젝트 시작' 단추를 보여 주는 스크린샷":::

"휴대폰 및 태블릿" 아래에서 "빈 활동" 프로젝트 템플릿을 선택합니다.

:::image type="content" source="../../media/android/studio-blank-activity.png" alt-text="프로젝트 템플릿 화면에서 선택한 '빈 활동' 옵션을 보여 주는 스크린샷":::

"API 26: Android 8.0(Oreo)" 이상의 최소 SDK를 선택합니다.

:::image type="content" source="../../media/android/studio-calling-min-api.png" alt-text="프로젝트 템플릿 화면에서 선택한 '빈 활동' 옵션을 보여 주는 스크린샷 2":::


### <a name="install-the-package"></a>패키지 설치

<!-- TODO: update with instructions on how to download, install and add package to project -->
프로젝트 수준 build.gradle을 찾고, `mavenCentral()`을 `buildscript` 및 `allprojects` 아래의 리포지토리 목록에 추가해야 합니다.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
그런 다음, 모듈 수준 build.gradle에서 dependencies 및 android 섹션에 다음 줄을 추가합니다.

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>애플리케이션 매니페스트에 권한 추가

전화를 거는 데 필요한 권한을 요청하려면 먼저 애플리케이션 매니페스트(`app/src/main/AndroidManifest.xml`)에서 해당 권한을 선언해야 합니다. 파일의 내용을 다음으로 바꿉니다.

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our Calling SDK depends on the Apache HTTP SDK.
When targeting Android SDK 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>앱 레이아웃 설정

두 개의 입력, 즉 수신자 ID에 대한 텍스트 입력 및 전화를 걸기 위한 단추가 필요합니다. 이러한 입력은 디자이너를 사용하거나 레이아웃 xml을 편집하여 추가할 수 있습니다. ID가 `call_button`이고 텍스트 입력이 `callee_id`인 단추를 만듭니다. `app/src/main/res/layout/activity_main.xml`로 이동하고, 파일의 내용을 다음으로 바꿉니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>기본 활동 스캐폴딩 및 바인딩 만들기

레이아웃이 만들어지면 바인딩과 활동의 기본 스캐폴딩을 추가할 수 있습니다. 이 활동은 런타임 권한을 요청하고, 통화 에이전트를 만들고, 단추가 눌러지면 전화를 겁니다. 각각에 대해 해당 섹션에서 설명합니다. `onCreate` 메서드는 `getAllPermissions` 및 `createAgent`를 호출하고 통화 단추에 대한 바인딩을 추가하도록 재정의됩니다. 이는 활동이 만들어질 때 한 번만 발생합니다. `onCreate`에 대한 자세한 내용은 [활동 수명 주기에 관한 이해](https://developer.android.com/guide/components/activities/activity-lifecycle) 가이드를 참조하세요.

**MainActivity.java** 로 이동하고, 내용을 다음 코드로 바꿉니다.

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import android.media.AudioManager;
import android.Manifest;
import android.content.pm.PackageManager;

import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
        
        setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
    }
}

```

### <a name="request-permissions-at-runtime"></a>런타임에 권한 요청

Android 6.0 이상(API 수준 23) 및 `targetSdkVersion` 23 이상에서는 앱이 설치될 때가 아니라 런타임에 권한이 부여됩니다. 이를 지원하기 위해 필요한 각 권한에 대해 `ActivityCompat.checkSelfPermission` 및 `ActivityCompat.requestPermissions`를 호출하도록 `getAllPermissions`를 구현할 수 있습니다.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> 앱을 설계할 때 언제 이러한 권한을 요청할지 해당 시기를 고려하세요. 권한은 미리 요청하는 것이 아니라 필요에 따라 요청해야 합니다. 자세한 내용은 [Android 권한 가이드](https://developer.android.com/training/permissions/requesting)를 참조하세요.

## <a name="object-model"></a>개체 모델

Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient는 Calling SDK의 주 진입점입니다.|
| CallAgent | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다. |
| CommunicationTokenCredential | CommunicationTokenCredential은 CallAgent를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.|
| CommunicationIdentifier | CommunicationIdentifier는 호출에 포함될 수 있는 다른 유형의 참가자로 사용됩니다.|

## <a name="create-an-agent-from-the-user-access-token"></a>사용자 액세스 토큰에서 에이전트 만들기

사용자 토큰을 사용하면 인증된 통화 에이전트를 인스턴스화할 수 있습니다. 일반적으로 이 토큰은 애플리케이션과 관련된 인증을 사용하는 서비스에서 생성됩니다. 사용자 액세스 토큰에 대한 자세한 내용은 [사용자 액세스 토큰](../../../access-tokens.md) 가이드를 확인하세요. 

빠른 시작에서는 `<User_Access_Token>`을 Azure Communication Services 리소스에 대해 생성된 사용자 액세스 토큰으로 바꿉니다.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>통화 에이전트를 사용하여 통화 시작

전화를 거는 것은 통화 에이전트를 통해 수행할 수 있으며, 수신자 ID 및 통화 옵션의 목록만 제공하면 됩니다. 빠른 시작에서는 비디오가 없는 기본 통화 옵션 및 텍스트 입력의 단일 수신자 ID가 사용됩니다.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.startCall(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>앱 시작 및 echo 봇 호출

이제 도구 모음의 "앱 실행" 단추(Shift+F10)를 사용하여 앱을 시작할 수 있습니다. `8:echo123`을 호출하여 전화를 걸 수 있는지 확인합니다. 미리 기록된 메시지가 재생된 다음, 해당 메시지를 사용자에게 다시 반복합니다.

:::image type="content" source="../../media/android/quickstart-android-call-echobot.png" alt-text="완료된 애플리케이션을 보여 주는 스크린샷":::
