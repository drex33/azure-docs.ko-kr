---
title: Android용 Azure Communication Services Teams Embed 사용
description: 이 개요에서는 Android용 Azure Communication Services Teams Embed 라이브러리를 사용하는 방법을 알아봅니다.
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5f74ab05517c2859cecb9913c19a9ab4c454c85c
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215130"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../access-tokens.md)에 대한 자세한 정보
- [애플리케이션에 Teams Embed 추가 시작](../getting-started-with-teams-embed.md)에 대한 빠른 시작을 완료합니다.

## <a name="joining-a-group-call"></a>그룹 통화 참가

`meetingUIClient.join` API에 `MeetingUIClientGroupCallLocator` 및 `MeetingUIClientJoinOptions`를 제공하여 그룹 통화에 참가할 수 있습니다. 그룹 통화는 다른 참가자에게 울리지 않습니다. 사용자는 자동으로 통화에 참가합니다.

기본 레이아웃 파일(`app/src/main/res/layout/activity_main.xml`)로 이동하여 그룹 통화에 참가하는 단추를 설정합니다. ID `join_groupCall`이 포함된 단추를 만듭니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_groupCall"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Group Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

**MainActivity.java** 로 이동하여 `onCreate` 메서드에서 클릭 시 단추를 추가합니다.

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;

import java.util.UUID;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        Button joinGroupCallButton = findViewById(R.id.join_groupCall);
        joinGroupCallButton.setOnClickListener(l -> joinGroupCall());
    }
}
```

클라이언트 설정 및 토큰 제공은 [빠른 시작](../getting-started-with-teams-embed.md)에 설명된 회의 참가 API와 동일한 방식으로 수행됩니다.

`joinGroupCall` 메서드는 *그룹 통화 참여* 단추를 누르면 수행되는 작업으로 설정됩니다. `MeetingUIClient`를 통해 그룹 통화에 참가할 수 있습니다. `MeetingUIClientGroupCallLocator`를 만들고 `MeetingUIClientJoinOptions`를 사용하여 참가 옵션을 구성합니다.
`<GROUP_ID>`를 UUID 문자열로 바꾸세요. 그룹 ID 문자열은 GUID 또는 UUID 형식이어야 합니다.

```java
/**
 * Join a group call with a groupID.
 */
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Teams Embed 통화 또는 모임 상태 이벤트 캡처

참여한 그룹 통화 또는 모임 상태는 `MeetingUIClientCallEventListener` 클래스에서 캡처할 수 있습니다. 상태에는 연결 상태, 참가자 수, 그리고 마이크 또는 카메라 상태와 같은 형식이 포함됩니다.

`MainActivity.java`에 다음 코드를 추가합니다.

```java
import androidx.core.content.ContextCompat;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;
import com.azure.android.communication.ui.meetings.MeetingUIClientCall;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallState;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProvider;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallIdentityProviderCallback;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallUserEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientGroupCallLocator;
import com.azure.android.communication.ui.meetings.MeetingUIClientIconType;
import com.azure.android.communication.ui.meetings.MeetingUIClientJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClientTeamsMeetingLinkLocator;
```

클래스에 `MeetingUIClientCallEventListener`를 추가합니다.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientEventListener {
```

통화 또는 모임에 성공적으로 참여하기 시작한 후 `setMeetingUIClientCallEventListener`를 매개 변수 `this`로 호출합니다.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);
    
    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);
    
    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

앱에 필요한 `MeetingUIClientCallEventListener` 메서드를 구현하고 필요하지 않은 메서드에는 스텁을 추가합니다.

```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to Connecting");
            break;
        case CONNECTED:
            System.out.println("Call state changed to Connected");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to Waiting in Lobby");
            break;
        case ENDED:
            System.out.println("Call state changed to Ended");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}

@Override
public void onIsMutedChanged() {
}

@Override
public void onIsSendingVideoChanged() {
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
}
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>앱에서 SDK 통화의 참가자로 고유한 ID를 가져옵니다.

앱은 통화 또는 모임의 참가자에게 사용자 ID 값을 할당하고 기본값을 재정의할 수 있습니다. 이 값에는 아바타, 이름 및 자막이 포함됩니다.

### <a name="assigning-avatars-for-call-participants"></a>통화 참가자용 아바타 할당

클래스에 `MeetingUIClientCallIdentityProvider`를 추가합니다.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallIdentityProvider {
```

매개 변수 `this`와 함께 `setMeetingUIClientCallIdentityProvider`를 호출합니다.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`provideAvatarFor` 메서드를 구현하고 각 `userIdentifier`를 해당 아바타에 매핑합니다.

```java
@Override
public void provideAvatarFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientAvatarSize avatarSize, MeetingUIClientCallIdentityProviderCallback callback) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        System.out.println("MeetingUIClientIdentityProvider.provideAvatarFor called for userIdentifier: " + userIdentifier.getId());
        if (userIdentifier.getId().startsWith("8:teamsvisitor:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_pink));
        } else if (userIdentifier.getId().startsWith("8:orgid:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_doctor_avatar));
        } else if (userIdentifier.getId().startsWith("8:acs:")) {
            callback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_green));
        }
    }
}
```

다른 필수 MeetingUIClientCallIdentityProvider 인터페이스 메서드를 클래스에 추가하면 빈 구현으로 남아 있을 수 있습니다.

```java
@Override
public void provideDisplayNameFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}

@Override
public void provideSubTitleFor(CommunicationIdentifier communicationIdentifier, MeetingUIClientCallIdentityProviderCallback callback) {
}
```
## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>UI의 사용자 동작에 대한 정보를 수신하고 사용자 지정 기능을 추가합니다.

### <a name="call-screen"></a>통화 화면

`MeetingUIClientCallUserEventListener` 인터페이스 메서드는 원격 참가자 프로필의 사용자 작업에 대해 호출됩니다.

클래스에 `MeetingUIClientCallUserEventListener`를 추가합니다.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallUserEventListener {
```

매개 변수 `this`와 함께 `setMeetingUIClientCallUserEventListener`를 호출합니다.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallUserEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
`onNamePlateOptionsClicked` 메서드를 추가 및 구현하고 각 `userIdentifier`를 해당 통화 참가 사용자에게 매핑합니다.
이 메서드는 통화 기본 화면에서 사용자 제목 텍스트를 한 번 탭하면 호출됩니다.

```java
@Override
public void onNamePlateOptionsClicked(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On user name plate clicked");
        }
    }
}
```

`onParticipantViewLongPressed` 메서드를 추가 및 구현하고 각 `userIdentifier`를 해당 통화 참가 사용자에게 매핑합니다.
이 메서드는 통화 기본 화면에서 사용자 타일을 길게 누를 때 호출됩니다. 사용자 지정 처리의 경우 `true`를 반환하고 길게 누르기의 기본 처리를 위한 `false`를 반환합니다.

```java
@Override
public boolean onParticipantViewLongPressed(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On participant tile long pressed");
            return true;
        }
        return false;
    }
}
```

### <a name="call-roster"></a>통화 명단

`MeetingUIClientCallRosterDelegate` 인터페이스 메서드는 통화 명단의 사용자 작업에 따라 호출됩니다.

클래스에 `MeetingUIClientCallRosterDelegate`를 추가합니다.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientCallRosterDelegate {
```

매개 변수 `this`와 함께 `setMeetingUIClientCallRosterDelegate`를 호출합니다.

```java
private void joinMeeting() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientTeamsMeetingLinkLocator meetingUIClientTeamsMeetingLinkLocator = new MeetingUIClientTeamsMeetingLinkLocator(<MEETING_URL>);

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        MeetingUIClientCall meetingUIClientCall = meetingUIClient.join(meetingUIClientTeamsMeetingLinkLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallRosterDelegate(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`onCallParticipantCellTapped` 메서드를 추가 및 구현하고 각 `userIdentifier`를 해당 통화 참가 사용자에게 매핑합니다.
이 메서드는 통화 명단 화면에서 원격 참가자 셀을 한 번 탭하면 호출됩니다. 사용자 지정 처리의 경우 `true`을 반환하고 단일 탭의 기본 처리를 위한 `false`를 반환합니다.

```java
@Override
public boolean onCallParticipantCellTapped(@NonNull Activity activity, @NonNull CommunicationIdentifier communicationIdentifier) {
    if(communicationIdentifier instanceof CommunicationUserIdentifier) {
        CommunicationUserIdentifier userIdentifier = (CommunicationUserIdentifier) communicationIdentifier;
        if (userIdentifier.getId().startsWith("8:acs:")) {
            // Custom behavior based on the user here.
            System.out.println("On call participant cell tapped");
            return true;
        }
        return false;
    }
}
```
## <a name="user-experience-customization"></a>사용자 환경 사용자 지정

SDK의 사용자 환경은 앱별 아이콘을 제공하여 사용자 지정할 수 있습니다. 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>통화 또는 모임의 UI 아이콘 사용자 지정
통화 또는 모임에 표시되는 아이콘은 `MeetingUIClient`에서 노출된 `public void setIconConfig(Map<MeetingUIClientIconType, Integer> iconConfig)` 메서드를 통해 사용자 지정할 수 있습니다.

meetingUIClient를 만든 후 `MeetingUIClientIconType`에서 지원되는 통화 아이콘의 `meetingUIClient.setIconConfig(getIconConfig())` 아이콘 구성을 설정합니다.

```java
private MeetingUIClient createMeetingUIClient() {
    MeetingUIClient meetingUIClient = new MeetingUIClient(credential);
    meetingUIClient.setIconConfig(getIconConfig());
}

private Map<IconType, Integer> getIconConfig() {
    Map<IconType, Integer> iconConfig = new HashMap<>();
    iconConfig.put(MeetingUIClientIconType.VIDEO_OFF, R.drawable.video_camera_off);
    iconConfig.put(MeetingUIClientIconType.VIDEO_ON, R.drawable.video_camera);
    iconConfig.put(MeetingUIClientIconType.MIC_ON, R.drawable.microphone_fill);
    iconConfig.put(MeetingUIClientIconType.MIC_OFF, R.drawable.microphone_off);
    iconConfig.put(MeetingUIClientIconType.MIC_PROHIBITED, R.drawable.mic_none);
    iconConfig.put(MeetingUIClientIconType.DEVICE_AUDIO, R.drawable.device_filled);
    iconConfig.put(MeetingUIClientIconType.SPEAKER, R.drawable.volume_high);
    iconConfig.put(MeetingUIClientIconType.SPEAKER_OFF, R.drawable.speaker_off);
    iconConfig.put(MeetingUIClientIconType.HEADSET, R.drawable.headset);
    iconConfig.put(MeetingUIClientIconType.BLUETOOTH, R.drawable.bluetooth_audio);
    iconConfig.put(MeetingUIClientIconType.HANGUP, R.drawable.close_app_bar);
    return iconConfig;
}
```

## <a name="perform-operations-with-the-call"></a>통화 관련 작업 수행

통화 제어 작업은 `MeetingUIClientCall`에 있는 메서드를 통해 노출됩니다.
이러한 메서드는 호출 작업을 제어하는 데 유용합니다.

MeetingUIClientCall에 대한 변수 추가

```java
public class MainActivity extends AppCompatActivity {
    private MeetingUIClientCall meetingUIClientCall;
```
참가 방법 반환 값에서 meetingUIClientCall 변수 할당

```java
private void joinGroupCall() {
    getAllPermissions();
    MeetingUIClient meetingUIClient = createMeetingUIClient();

    MeetingUIClientJoinOptions meetingJoinOptions = new MeetingUIClientJoinOptions(displayName, false);

    try {
        UUID groupUUID = UUID.fromString("<GROUP_ID>");
        MeetingUIClientGroupCallLocator meetingUIClientGroupCallLocator = new MeetingUIClientGroupCallLocator(groupUUID);
        meetingUIClientCall = meetingUIClient.join(meetingUIClientGroupCallLocator, meetingJoinOptions);
        meetingUIClientCall.setMeetingUIClientCallEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join group call: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

`mute` 메서드를 호출하여 진행 중인 통화(있는 경우)에서 마이크를 음소거합니다.
마이크 상태는 `MeetingUIClientCallEventListener`의 `onIsMutedChanged` 메서드 내용에 따라 변경됩니다.

```java
// Mute the microphone for an active call.
public void mute() {
    try {
        meetingUIClientCall.mute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Mute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`unmute` 메서드를 호출하여 진행 중인 통화(있는 경우)에서 마이크의 음소거를 해제합니다.

```java
// Unmute the microphone for an active call.
public void unmute() {
    try {
        meetingUIClientCall.unmute();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Unmute call failed: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>다른 작업은 `MeetingUIClientCall` 클래스에서 확인할 수 있습니다.

```java
// Start the video for an active call.
public void startVideo()

// Stop the video for an active call.
public void stopVideo()

// Set the preferred audio route in the call for self user.
public void setAudioRoute(MeetingUIClientAudioRoute audioRoute)

// Raise the hand of current user for an active call.
public void raiseHand()

// Lower the hand of current user for an active call.
public void lowerHand()

// Change the layout in the call for self user.
public List<MicrosoftTeamsSDKLayoutMode> getSupportedLayoutModes()
public void changeLayout(MeetingUIClientLayoutMode microsoftTeamsSDKLayoutMode)

// Hang up the call or leave the meeting.
public void hangUp()
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>동일한 앱에서 Teams Embed SDK와 Azure Communication Calling SDK 사용

Teams Embed SDK는 ACS(Azure Communication Calling SDK)도 제공하므로 동일한 앱에서 두 SDK 기능을 모두 사용할 수 있습니다. 한 번에 하나의 SDK만 초기화하고 사용할 수 있습니다. 두 SDK를 모두 초기화하고 동시에 사용하면 예기치 않은 동작이 발생합니다. 

다음 가져오기를 클래스에 추가합니다.
```java
import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.CallState;
import com.azure.android.communication.calling.GroupCallLocator;
import com.azure.android.communication.calling.JoinCallOptions;
import com.azure.android.communication.common.CommunicationTokenCredential;
```

ACS 사용에 대한 변수 선언
```java
public class MainActivity extends AppCompatActivity {
    private CallAgent agent;
    private CallClient mCallClient;
    private Call mCall;
```

초기화는 새 `CallClient`를 만들어 수행합니다. 만든 항목을 `joinAcsCall` 또는 다른 메서드에 추가합니다.

```java
private void joinAcsCall() {
    if (mCallClient == null) {
        mCallClient = new CallClient();
    }
}
```
해당 설명서에 설명된 것처럼 모든 ACS API를 사용합니다. API 사용에 대해서는 이 설명서에서 다루지 않습니다. 

사용이 더 이상 필요하지 않거나 앱이 Teams Embed SDK를 사용해야 하는 경우 ACS SDK를 삭제하고 `null`을 해당 변수로 설정합니다.
```java
private void stopAcs() {
    mCall = null;
    agent.dispose();
    agent = null;
    mCallClient.dispose();
    mCallClient = null;
}
```

`MeetingUIClient`를 만드는 동안에는 Teams Embed SDK 초기화도 이루어집니다. 만든 항목을 `createMeetingUIClient` 또는 다른 메서드에 추가합니다.
```java
private MeetingUIClient createMeetingUIClient() { 
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        return new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting ui client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```
해당 설명서에 설명된 것처럼 Teams Embed SDK API를 사용합니다. API 사용에 대해서는 이 설명서에서 다루지 않습니다. 

사용이 더 이상 필요하지 않거나 앱이 ACS SDK를 사용해야 하는 경우 Teams Embed SDK를 삭제하고 `null`을 해당 변수로 설정합니다.
```java
private void disposeTeamsSdk() {
    try {
        meetingUIClient.dispose();
        meetingUIClientCall = null;
        meetingUIClient = null;
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to teardown Teams Sdk: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

Teams Embed SDK 삭제는 통화가 진행 중이 아닐 때만 가능합니다. `meetingUIClient.dispose()`은 활성 호출이 있는 경우 예외를 throw하여 반환합니다. 현재 호출을 중단하고 `disposeTeamsSdk()`를 호출합니다.

```java
private void endMeeting() {
    try {
        meetingUIClientCall.hangUp();
        disposeTeamsSdk();
    } catch (Exception ex) {
        Toast.makeText(getContext(), "Failed to end meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`MeetingUIClientCallEventListener` 인터페이스 메서드 `onCallStateChanged(MeetingUIClientCallState callState)`를 구현하여 종료 중인 통화에 대한 상태 업데이트를 가져오고 이후 Teams Embed SDK를 삭제합니다.
```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case ENDED:
            disposeTeamsSdk();
            break;
        default:
        System.out.println("Call state changed to: " + callState.toString());
    }
}
```

클래스에 다른 필수 `MeetingUIClientCallEventListener` 인터페이스 메서드 추가

```java
@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count has changed to: " + newCount);
}

@Override
public void onIsMutedChanged() {
    System.out.println("Mute state changed to: " + meetingUIClientCall.isMuted());
}

@Override
public void onIsSendingVideoChanged() {
    System.out.println("Sending video state changed to: " + meetingUIClientCall.isSendingVideo());
}

@Override
public void onIsHandRaisedChanged(List<String> participantIds) {
    System.out.println("Self participant raise hand status changed to: " + meetingUIClientCall.isHandRaised());
}
```
