---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: d53c9a7d265366531cc22d6de726f84ddb9f7a50
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910139"
---
Communication Services 호출 클라이언트 라이브러리를 사용하여 앱에 1:1 영상 통화를 추가하여 Azure Communication Services를 시작하세요. Android용 Azure Communication Services Calling SDK를 사용하여 영상 통화를 시작 및 응답하는 방법에 대해 알아봅니다.

## <a name="sample-code"></a>샘플 코드

끝으로 건너뛰려면 [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/videoCallingQuickstart)에서 샘플 앱을 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android 스튜디오](https://developer.android.com/studio)(Android 애플리케이션 만들기용)
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- [사용자 액세스 토큰](../../../access-tokens.md)(Azure Communication Service용)

### <a name="create-an-android-app-with-an-empty-activity"></a>빈 활동을 사용하여 Android 앱 만들기

Android 스튜디오에서 [새 Android 스튜디오 프로젝트 시작]을 선택합니다.

:::image type="content" source="../../media/android/studio-new-project.png" alt-text="Android 스튜디오에서 선택한 '새 Android 스튜디오 프로젝트 시작' 단추를 보여 주는 스크린샷":::

"휴대폰 및 태블릿" 아래에서 "빈 활동" 프로젝트 템플릿을 선택합니다.

:::image type="content" source="../../media/android/studio-blank-activity.png" alt-text="프로젝트 템플릿 화면에서 선택한 '빈 활동' 옵션을 보여 주는 스크린샷":::

"API 26: Android 8.0(Oreo)" 이상의 최소 SDK를 선택합니다.

:::image type="content" source="../../media/android/studio-calling-min-api.png" alt-text="프로젝트 템플릿 화면에서 선택한 '빈 활동' 옵션을 보여 주는 스크린샷 2":::

### <a name="install-the-package"></a>패키지 설치

프로젝트 수준 `build.gradle`을 찾고, `mavenCentral()`을 `buildscript` 및 `allprojects` 아래의 리포지토리 목록에 추가해야 합니다.
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
그런 다음, 모듈 수준 `build.gradle`에서 dependencies 및 android 섹션에 다음 줄을 추가합니다.

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
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
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

수신자 ID에 대한 텍스트 입력, 전화를 걸기 위한 단추 및 전화를 끊기 위한 단추가 필요합니다. 또한 로컬 비디오를 켜고 끌 수 있는 두 개의 단추가 필요합니다. 로컬 및 원격 비디오 스트림용 2개의 컨테이너를 배치해야 합니다. 이러한 입력은 디자이너를 사용하거나 레이아웃 xml을 편집하여 추가할 수 있습니다. `app/src/main/res/layout/activity_main.xml`로 이동하고, 파일의 내용을 다음으로 바꿉니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical">

            <EditText
                android:id="@+id/callee_id"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:gravity="center"
                android:hint="Callee Id"
                android:inputType="textPersonName"
                app:layout_constraintBottom_toTopOf="@+id/call_button"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                app:layout_constraintVertical_bias="0.064" />
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content">
                <Button
                    android:id="@+id/call_button"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Call"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/show_preview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Show Video"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/hide_preview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Hide Video"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
                <Button
                    android:id="@+id/hang_up"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginBottom="16dp"
                    android:gravity="center"
                    android:text="Hang Up"
                    app:layout_constraintBottom_toBottomOf="parent"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toStartOf="parent" />
            </LinearLayout>
            <FrameLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent">
                <LinearLayout
                    android:id="@+id/remotevideocontainer"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:gravity="end"
                    android:orientation="horizontal"
                    android:padding="10dp"></LinearLayout>
                <LinearLayout
                    android:id="@+id/localvideocontainer"
                    android:layout_width="180dp"
                    android:layout_height="320dp"
                    android:layout_gravity="right|bottom"
                    android:orientation="horizontal"
                    android:padding="10dp"></LinearLayout>

            </FrameLayout>
        </LinearLayout>
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>기본 활동 스캐폴딩 및 바인딩 만들기

레이아웃이 만들어지면 바인딩과 활동의 기본 스캐폴딩을 추가할 수 있습니다. 이 활동은 런타임 권한을 요청하고, 통화 에이전트를 만들고, 단추가 눌러지면 전화를 겁니다. 각각에 대해 해당 섹션에서 설명합니다. `onCreate` 메서드는 `getAllPermissions` 및 `createAgent`를 호출하고 통화 단추에 대한 바인딩을 추가하도록 재정의됩니다. 이는 활동이 만들어질 때 한 번만 발생합니다. `onCreate`에 대한 자세한 내용은 [활동 수명 주기에 관한 이해](https://developer.android.com/guide/components/activities/activity-lifecycle) 가이드를 참조하세요.

**MainActivity.java** 로 이동하고, 내용을 다음 코드로 바꿉니다.

```java
package com.example.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.media.AudioManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import android.widget.LinearLayout;
import android.content.Context;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.CallingCommunicationException;
import com.azure.android.communication.calling.CameraFacing;
import com.azure.android.communication.calling.ParticipantsUpdatedListener;
import com.azure.android.communication.calling.PropertyChangedEvent;
import com.azure.android.communication.calling.PropertyChangedListener;
import com.azure.android.communication.calling.VideoDeviceInfo;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;
import com.azure.android.communication.calling.DeviceManager;
import com.azure.android.communication.calling.VideoOptions;
import com.azure.android.communication.calling.LocalVideoStream;
import com.azure.android.communication.calling.VideoStreamRenderer;
import com.azure.android.communication.calling.VideoStreamRendererView;
import com.azure.android.communication.calling.CreateViewOptions;
import com.azure.android.communication.calling.ScalingMode;
import com.azure.android.communication.calling.IncomingCall;
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.AcceptCallOptions;
import com.azure.android.communication.calling.ParticipantsUpdatedEvent;
import com.azure.android.communication.calling.RemoteParticipant;
import com.azure.android.communication.calling.RemoteVideoStream;
import com.azure.android.communication.calling.RemoteVideoStreamsEvent;
import com.azure.android.communication.calling.RendererListener;


import java.util.ArrayList;
import java.util.List;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;

public class MainActivity extends AppCompatActivity {

    private CallAgent callAgent;
    private LocalVideoStream currentVideoStream;
    private DeviceManager deviceManager;
    private IncomingCall incomingCall;
    private Call call;
    VideoStreamRenderer previewRenderer;
    VideoStreamRendererView preview;
    final Map<Integer, StreamData> streamData = new HashMap<>();
    private boolean renderRemoteVideo = true;
    private ParticipantsUpdatedListener remoteParticipantUpdatedListener;
    private PropertyChangedListener onStateChangedListener;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();

        handleIncomingCall();

        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
        Button hangupButton = findViewById(R.id.hang_up);
        hangupButton.setOnClickListener(l -> hangUp());
        Button startVideo = findViewById(R.id.show_preview);
        startVideo.setOnClickListener(l -> turnOnLocalVideo());
        Button stopVideo = findViewById(R.id.hide_preview);
        stopVideo.setOnClickListener(l -> turnOffLocalVideo());
        
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

    /**
     * Handle incoming calls
     */
    private void handleIncomingCall() {
        // See section on answering incoming call
    }

    /**
     * Mid-call operations
     */
    public void turnOnLocalVideo() {
        // See setion on 
    }
    public void turnOffLocalVideo() {
        
    }

    /**
     * End calls
     */
    private void hangUp() {
        // See section on ending the call
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

인증된 통화 에이전트를 만들려면 사용자 토큰이 필요합니다. 일반적으로 이 토큰은 애플리케이션과 관련된 인증을 사용하는 서비스에서 생성됩니다. 사용자 액세스 토큰에 대한 자세한 내용은 [사용자 액세스 토큰](../../../access-tokens.md) 가이드를 확인하세요. 

빠른 시작에서는 `<User_Access_Token>`을 Azure Communication Services 리소스에 대해 생성된 사용자 액세스 토큰으로 바꿉니다.

```java
/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    Context context = this.getApplicationContext();
    String userToken = "<User_Access_Token>";
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        CallClient callClient = new CallClient();
        deviceManager = callClient.getDeviceManager(context).get();
        callAgent = callClient.createCallAgent(getApplicationContext(), credential).get(); 
    } catch (Exception ex) {
        Toast.makeText(context, "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}
```
## <a name="start-a-video-call-using-the-call-agent"></a>통화 에이전트를 사용하여 영상 통화를 시작합니다.

전화를 거는 것은 통화 에이전트를 통해 수행할 수 있으며, 수신자 ID 및 통화 옵션의 목록만 제공하면 됩니다. 

영상 통화를 걸려면 `deviceManager` `getCameras` API를 사용하여 로컬 카메라를 열거해야 합니다. 원하는 카메라를 선택한 후에는 해당 카메라를 사용하여 `LocalVideoStream` 인스턴스를 생성하고 call 메서드에 대한 `localVideoStream` 배열의 항목으로 `videoOptions`에 전달합니다. 전화가 연결되면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작합니다.

```java
private void startCall() {
    Context context = this.getApplicationContext();
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    ArrayList<CommunicationIdentifier> participants = new ArrayList<CommunicationIdentifier>();
    StartCallOptions options = new StartCallOptions();
    List<VideoDeviceInfo> cameras = deviceManager.getCameras();
    if(!cameras.isEmpty()) {
        VideoDeviceInfo camera = chooseCamera(cameras);
        currentVideoStream = new LocalVideoStream(camera, context);
        LocalVideoStream[] videoStreams = new LocalVideoStream[1];
        videoStreams[0] = currentVideoStream;
        VideoOptions videoOptions = new VideoOptions(videoStreams);
        options.setVideoOptions(videoOptions);
        showPreview(currentVideoStream);
    }
    participants.add(new CommunicationUserIdentifier(calleeId));

    call = callAgent.startCall(
            context,
            participants,
            options);
    
    //Subcribe to events on updates of call state and remote participants
    remoteParticipantUpdatedListener = this::handleRemoteParticipantsUpdate;
    onStateChangedListener = this::handleCallOnStateChanged;
    call.addOnRemoteParticipantsUpdatedListener(remoteParticipantUpdatedListener);
    call.addOnStateChangedListener(onStateChangedListener);
}
```

이 빠른 시작에서는 디바이스의 전면 카메라를 사용하는 것이 좋습니다. `chooseCamera` 기능은 카메라 열거를 입력으로 사용합니다. 전면 카메라를 사용할 수 없는 경우 사용 가능한 첫 번째 카메라를 사용합니다. `Start Call`을 탭할 때 사용 가능한 카메라가 없으면 음성 통화가 시작됩니다. 그러나 원격 참가자가 비디오로 응답한 경우에도 원격 비디오 스트림을 볼 수 있습니다. 

```java
VideoDeviceInfo chooseCamera(List<VideoDeviceInfo> cameras) {
    for (VideoDeviceInfo camera : cameras) {
        if (camera.getCameraFacing() == CameraFacing.FRONT) {
            return camera;
        }
    }
    return cameras.get(0);
}
```

`LocalVideoStream` 인스턴스를 생성한 후에는 렌더러를 만들어 UI에 표시할 수 있습니다. 

```java
private void showPreview(LocalVideoStream stream) {
    previewRenderer = new VideoStreamRenderer(stream, this);
    LinearLayout layout = ((LinearLayout)findViewById(R.id.localvideocontainer));
    preview = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
    runOnUiThread(() -> {
        layout.addView(preview);
    });
}
```

## <a name="accept-an-incoming-call"></a>수신 전화 받기

수신 전화는 `callAgent`에서 `addOnIncomingCallListener`를 구독하여 가져올 수 있습니다. 

```java
private void handleIncomingCall() {
    callAgent.addOnIncomingCallListener((incomingCall) -> {
        this.incomingCall = incomingCall;
        Executors.newCachedThreadPool().submit(this::answerIncomingCall);
    });
}
```

비디오 카메라로 통화하려면 deviceManager getCameras API를 사용하여 로컬 카메라를 열거하고 카메라를 선택하고 `call` 개체에서 'accept' 메서드를 호출하기 전에 `LocalVideoStream` 인스턴스를 생성하고 `acceptCallOptions`로 전달합니다. 
```java
private void answerIncomingCall() {
    Context context = this.getApplicationContext();
    if (incomingCall == null){
        return;
    }
    AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
    List<VideoDeviceInfo> cameras = deviceManager.getCameras();
    if(!cameras.isEmpty()) {
        VideoDeviceInfo camera = chooseCamera(cameras);
        currentVideoStream = new LocalVideoStream(camera, context);
        LocalVideoStream[] videoStreams = new LocalVideoStream[1];
        videoStreams[0] = currentVideoStream;
        VideoOptions videoOptions = new VideoOptions(videoStreams);
        acceptCallOptions.setVideoOptions(videoOptions);
        showPreview(currentVideoStream);
    }
    try {
        call = incomingCall.accept(context, acceptCallOptions).get();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } catch (ExecutionException e) {
        e.printStackTrace();
    }

    //Subcribe to events on updates of call state and remote participants
    remoteParticipantUpdatedListener = this::handleRemoteParticipantsUpdate;
    onStateChangedListener = this::handleCallOnStateChanged;
    call.addOnRemoteParticipantsUpdatedListener(remoteParticipantUpdatedListener);
    call.addOnStateChangedListener(onStateChangedListener);
}
```

## <a name="remote-participant-and-remote-video-streams"></a>원격 참가자 및 원격 비디오 스트림

통화를 시작하거나 수신 전화에 응답할 때 `addOnRemoteParticipantsUpdatedListener` 이벤트를 구독하여 원격 참가자를 처리해야 합니다. 

```java
remoteParticipantUpdatedListener = this::handleRemoteParticipantsUpdate;
call.addOnRemoteParticipantsUpdatedListener(remoteParticipantUpdatedListener);
```
동일한 클래스 내에 정의된 이벤트 수신기를 사용하는 경우 수신기를 변수에 바인딩합니다. 변수를 인수로 전달하여 수신기 메서드를 추가하고 제거합니다.

수신기를 인수로 직접 전달하려고 하면 해당 수신기에 대한 참조가 손실됩니다. Java는 이러한 수신기의 새 인스턴스를 만들고 이전에 만든 인스턴스를 참조하지 않습니다. 이러한 인스턴스는 계속 올바르게 시작되지만 더 이상 참조가 없으므로 제거될 수 없습니다.

### <a name="remote-participant-and-remote-video-stream-update"></a>원격 참가자 및 원격 비디오 스트림 업데이트

현재 1:1 통화를 구현하고 있기 때문에 이 빠른 시작에서는 추가된 참가자만 처리합니다. 원격 참가자가 제거되면 통화가 종료됩니다. 추가된 참가자의 경우 `addOnVideoStreamsUpdatedListener`를 구독하여 비디오 스트림 업데이트를 처리합니다. 

```java
public void handleRemoteParticipantsUpdate(ParticipantsUpdatedEvent args) {
    LinearLayout participantVideoContainer = findViewById(R.id.remotevideocontainer);
    handleAddedParticipants(args.getAddedParticipants(),participantVideoContainer);
}

private void handleAddedParticipants(List<RemoteParticipant> participants, LinearLayout participantVideoContainer) {
    for (RemoteParticipant remoteParticipant : participants) {
        remoteParticipant.addOnVideoStreamsUpdatedListener(videoStreamsEventArgs -> videoStreamsUpdated(videoStreamsEventArgs));
    }
}

private void videoStreamsUpdated(RemoteVideoStreamsEvent videoStreamsEventArgs) {
    for(RemoteVideoStream stream : videoStreamsEventArgs.getAddedRemoteVideoStreams()) {
        StreamData data = new StreamData(stream, null, null);
        streamData.put(stream.getId(), data);
        if (renderRemoteVideo) {
            startRenderingVideo(data);
        }
    }

    for(RemoteVideoStream stream : videoStreamsEventArgs.getRemovedRemoteVideoStreams()) {
        stopRenderingVideo(stream);
    }
}
```

### <a name="render-remote-videos"></a>원격 비디오 렌더링

원격 비디오 스트림의 렌더러를 만들고 보기에 연결하여 원격 보기 렌더링을 시작합니다. 렌더링을 중지하려면 보기를 삭제합니다. 

```java
void startRenderingVideo(StreamData data){
    if (data.renderer != null) {
        return;
    }
    LinearLayout layout = ((LinearLayout)findViewById(R.id.remotevideocontainer));
    data.renderer = new VideoStreamRenderer(data.stream, this);
    data.renderer.addRendererListener(new RendererListener() {
        @Override
        public void onFirstFrameRendered() {
            String text = data.renderer.getSize().toString();
            Log.i("MainActivity", "Video rendering at: " + text);
        }

        @Override
        public void onRendererFailedToStart() {
            String text = "Video failed to render";
            Log.i("MainActivity", text);
        }
    });
    data.rendererView = data.renderer.createView(new CreateViewOptions(ScalingMode.FIT));
    runOnUiThread(() -> {
        layout.addView(data.rendererView);
    });
}

void stopRenderingVideo(RemoteVideoStream stream) {
    StreamData data = streamData.get(stream.getId());
    if (data == null || data.renderer == null) {
        return;
    }
    runOnUiThread(() -> {
        ((LinearLayout) findViewById(R.id.remotevideocontainer)).removeAllViews();
    });
    data.rendererView = null;
    // Dispose renderer
    data.renderer.dispose();
    data.renderer = null;
}

static class StreamData {
    RemoteVideoStream stream;
    VideoStreamRenderer renderer;
    VideoStreamRendererView rendererView;
    StreamData(RemoteVideoStream stream, VideoStreamRenderer renderer, VideoStreamRendererView rendererView) {
        this.stream = stream;
        this.renderer = renderer;
        this.rendererView = rendererView;
    }
}
```

## <a name="call-state-update"></a>통화 상태 업데이트
이 빠른 시작에서는 `CallState`의 변경 사항을 처리합니다. 통화가 연결되면 원격 참가자를 처리하고 통화 연결이 끊어지면 `previewRenderer`를 삭제하여 로컬 비디오를 중지합니다. 

```groovy
private void handleCallOnStateChanged(PropertyChangedEvent args) {
    if (call.getState() == CallState.CONNECTED) {
        LinearLayout participantVideoContainer = findViewById(R.id.remotevideocontainer);
        handleAddedParticipants(call.getRemoteParticipants(),participantVideoContainer);
    }
    if (call.getState() == CallState.DISCONNECTED) {
        if (previewRenderer != null) {
            previewRenderer.dispose();
        }
    }
}
```

## <a name="end-a-call"></a>통화 종료
통화 인스턴스에서 `hangUp()` 함수를 호출하여 통화를 종료하고 `previewRenderer`를 삭제하여 로컬 비디오를 중지합니다. 
```java
private void hangUp() {
    try {
        call.hangUp().get();
    } catch (ExecutionException | InterruptedException e) {
        e.printStackTrace();
    }
    if (previewRenderer != null) {
        previewRenderer.dispose();
    }
}
```

## <a name="run-the-code"></a>코드 실행

이제 Android Studio의 도구 모음에서 `Run 'App'` 단추를 사용하여 앱을 시작할 수 있습니다. 
