---
title: Azure Communication Services - 알려진 문제
description: Azure Communication Services에 대해 알아봅니다.
author: rinarish
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 73a30e7052faaaa644511fec65796a4575e5ab4a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259135"
---
# <a name="known-issues"></a>알려진 문제
이 문서에서는 Azure Communication Services Calling SDK 및 Azure Communication Services Call Automation API와 관련된 제한 사항 및 알려진 문제에 대한 정보를 제공합니다.

> [!IMPORTANT]
> 통화 환경의 품질에 영향을 줄 수 있는 여러 요인이 있습니다. Communication Services 네트워크 구성 및 테스트 모범 사례에 대한 자세한 내용은 **[네트워크 요구 사항](./voice-video-calling/network-requirements.md)** 설명서를 참조하세요.

## <a name="azure-communication-services-calling-sdks"></a>Azure Communication Services Calling SDK

### <a name="javascript-sdk"></a>JavaScript SDK

이 섹션에서는 Azure Communication Services JavaScript 음성 및 비디오 통화 SDK와 관련된 알려진 문제에 대한 정보를 제공합니다.

#### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>페이지를 새로 고치면 통화에서 사용자가 즉시 제거되지 않습니다.

사용자가 통화에 있고 페이지를 새로 고치도록 결정한 경우 Communication Services 미디어 서비스는 통화에서 이 사용자를 즉시 제거하지 않습니다. 사용자가 다시 조인할 때까지 기다립니다. 미디어 서비스 시간이 초과된 후에 사용자가 통화에서 제거됩니다.

통화하는 동안 최종 사용자가 애플리케이션의 페이지를 새로 고칠 필요가 없는 사용자 환경을 빌드하는 것이 가장 좋습니다. 사용자가 페이지를 새로 고치면 애플리케이션으로 다시 돌아간 후 동일한 Communication Services 사용자 ID를 다시 사용합니다.

통화 중 다른 참가자의 관점에서 사용자는 1~2분간 통화에 남아 있게 됩니다.

사용자가 동일한 Communication Services 사용자 ID를 사용하여 다시 조인하는 경우 `remoteParticipants` 컬렉션에 동일한 기존 개체로 표시됩니다.

사용자가 새로 고침을 수행하기 전에 비디오를 전송한 경우 `videoStreams` 컬렉션은 서비스가 시간 초과되고 제거될 때까지 이전 스트림 정보를 유지합니다. 이 시나리오에서 애플리케이션은 컬렉션에 추가된 새 스트림을 관찰하고 가장 높은 `id`로 스트림을 렌더링하도록 결정할 수도 있습니다. 


#### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>웹의 여러 디바이스에서 여러 미리 보기를 렌더링하는 것은 불가능합니다.
이 문제는 알려진 제한 사항입니다. 자세한 내용은 [Calling SDK 개요](./voice-video-calling/calling-sdk-features.md)를 참조하세요.

#### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>애플리케이션이 iOS 또는 iPadOS에서 실행되는 경우 Safari에서 디바이스를 열거할 수 없습니다.

애플리케이션은 Safari iOS/iPad에서 마이크/스피커 디바이스(예: Bluetooth)를 열거/선택할 수 없습니다. 이는 알려진 운영 체제 제한 사항입니다.

macOS에서 Safari를 사용하는 경우 앱은 Communication Services 디바이스 관리자를 통해 스피커를 열거/선택할 수 없습니다. 이 시나리오에서는 OS를 통해 디바이스를 선택해야 합니다. macOS에서 Chrome을 사용하는 경우 앱은 Communication Services 디바이스 관리자를 통해 디바이스를 열거/선택할 수 있습니다.

#### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>진행 중인 VoIP 통화 중에 SMS 메시지 또는 통화를 받을 때 오디오 연결이 끊어졌습니다.
이 문제는 여러 가지 이유로 인해 발생할 수 있습니다.

- 일부 모바일 브라우저는 백그라운드 상태에서 연결을 유지하지 않습니다. 이렇게 하면 애플리케이션을 백그라운드에 푸시하는 이벤트에 의해 VoIP 통화가 중단된 경우 통화 환경이 저하될 수 있습니다. 
- 경우에 따라 SMS 또는 PSTN 통화가 오디오 사운드를 캡처하고, 음성을 VoIP 통화로 다시 릴리스하지 않습니다. Apple은 iOS 버전 14.4.1+에서 이 문제를 해결했습니다. 

<br/>클라이언트 라이브러리: 통화(JavaScript)
<br/>브라우저: Safari, Chrome
<br/>운영 체제: iOS, Android

#### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>비디오 디바이스를 반복적으로 전환하면 비디오 스트리밍이 일시적으로 중지될 수 있습니다.

비디오 디바이스 간을 전환하면 선택한 디바이스에서 스트림을 획득하는 동안 비디오 스트림이 일시 중지될 수 있습니다.

##### <a name="possible-causes"></a>가능한 원인
디바이스 간을 자주 전환하면 성능이 저하될 수 있습니다. 개발자는 다른 디바이스 스트림을 시작하기 전에 중지하는 것이 좋습니다.

#### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Bluetooth 헤드셋 마이크가 검색되지 않으므로 iOS의 Safari에서 통화 중에는 소리가 발생하지 않습니다.
Bluetooth 헤드셋은 iOS의 Safari에서 지원되지 않습니다. Bluetooth 디바이스는 사용 가능한 마이크 옵션에 나열되지 않으며, Safari를 통해 Bluetooth를 사용하는 경우 다른 참가자가 사용자의 소리를 듣지 못합니다.

##### <a name="possible-causes"></a>가능한 원인
이는 알려진 macOS/iOS/iPadOS 운영 체제 제한 사항입니다. 

**macOS** 및 **iOS/iPadOS** 에서 Safari를 사용하는 경우 Safari에서 스피커 열거/선택이 지원되지 않으므로 Communication Services 디바이스 관리자를 통해 스피커 디바이스를 열거/선택할 수 없습니다. 이 시나리오에서는 운영 체제를 통해 디바이스 선택을 업데이트해야 합니다.

#### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>디바이스의 회전으로 비디오 품질이 떨어질 수 있습니다.
사용자가 디바이스를 회전할 때 비디오 품질이 저하될 수 있습니다.

<br/>영향을 받는 디바이스: Google Pixel 5, Google Pixel 3a, Apple iPad 8 및 Apple iPad X
<br/>클라이언트 라이브러리: 통화(JavaScript)
<br/>브라우저: Safari, Chrome
<br/>운영 체제: iOS, Android


#### <a name="camera-switching-makes-the-screen-freeze"></a>카메라 전환으로 인해 화면이 동결됩니다. 
Communication Services 사용자가 JavaScript 통화 SDK를 사용하여 통화에 조인한 다음, 카메라 스위치 단추를 누르면 애플리케이션을 새로 고치거나 사용자가 브라우저를 백그라운드에 푸시할 때까지 UI가 응답하지 않을 수 있습니다.

<br/>영향을 받는 디바이스: Google Pixel 4a
<br/>클라이언트 라이브러리: 통화(JavaScript)
<br/>브라우저: Chrome
<br/>운영 체제: iOS, Android


##### <a name="possible-causes"></a>가능한 원인
확인 중입니다.

#### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>통화가 "연결 중" 상태에 있는 동안 비디오 신호가 중지된 경우 통화가 시작된 후 비디오가 전송되지 않습니다. 
통화가 `Connecting` 상태에 있는 동안 사용자가 신속하게 비디오를 설정/해제하기로 결정한 경우 이로 인해 통화에 대해 얻은 스트림과 관련된 문제가 발생할 수 있습니다. 통화가 `Connecting` 상태에 있는 동안 비디오를 설정/해제하지 않아도 되는 방식으로 개발자가 앱을 빌드하는 것이 좋습니다. 이 문제로 인해 다음과 같은 시나리오에서 비디오 성능이 저하될 수 있습니다.

 - 통화가 `Connecting` 상태에 있는 동안 사용자가 오디오로 시작한 다음, 비디오를 시작하고 중지하는 경우
 - 통화가 `Lobby` 상태에 있는 동안 사용자가 오디오로 시작한 다음, 비디오를 시작하고 중지하는 경우

##### <a name="possible-causes"></a>가능한 원인
확인 중입니다.

#### <a name="enumeratingaccessing-devices-for-safari-on-macos-and-ios"></a>MacOS 및 iOS에서 Safari용 디바이스 열거/액세스 
디바이스에 대한 액세스 권한이 부여되면 일정 시간 후에 디바이스 사용 권한이 초기화됩니다. MacOS 및 iOS의 Safari는 스트림을 획득하지 않는 한 매우 긴 시간 동안 사용 권한을 유지하지 않습니다. 이 문제를 해결하는 가장 간단한 방법은 디바이스 관리자의 디바이스 열거형 API(DeviceManager.getCameras(), DeviceManager.getSpeakers() 및 DeviceManager.getMicrophones())를 호출하기 전에 DeviceManager.askDevicePermission() API를 호출하는 것입니다. 사용 권한이 있는 경우 사용자에게 아무것도 표시되지 않습니다. 없는 경우 메시지가 다시 표시됩니다.

<br/>영향을 받는 디바이스: iPhone
<br/>클라이언트 라이브러리: 통화(JavaScript)
<br/>브라우저: Safari
<br/>운영 체제: iOS

####  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>원격 참가자 비디오를 렌더링하는 데 시간이 오래 걸리는 경우가 있습니다.
진행 중인 그룹 통화 동안 _사용자 A_ 가 비디오를 보낸 다음, _사용자 B_ 가 통화에 조인합니다. 사용자 B에게 사용자 A의 비디오가 표시되지 않거나 사용자 A의 비디오가 긴 지연 후 렌더링을 시작하는 경우가 있습니다. 이 문제는 추가 구성이 필요한 네트워크 환경에 의해 발생할 수 있습니다. 네트워크 구성 지침은 [네트워크 요구 사항](./voice-video-calling/network-requirements.md) 설명서를 참조하세요.

#### <a name="using-3rd-party-libraries-to-access-gum-during-the-call-may-result-in-audio-loss"></a>통화 중에 타사 라이브러리를 사용하여 GUM에 액세스하면 오디오 손실이 발생할 수 있습니다.
애플리케이션 내에서 getUserMedia를 별도로 사용하면 타사 라이브러리가 ACS 라이브러리의 디바이스 액세스를 인수하므로 오디오 스트림이 손실됩니다.
개발자는 다음을 수행하는 것이 좋습니다.
1. 통화 중에 내부적으로 GetUserMedia API를 사용하는 타사 라이브러리를 사용하지 마세요.
2. 타사 라이브러리를 계속 사용해야 하는 경우 복구할 수 있는 유일한 방법은 선택한 디바이스를 변경하거나(사용자에게 두 개 이상의 디바이스가 있는 경우), 통화를 다시 시작하는 것입니다.

<br/>브라우저: Safari
<br/>운영 체제: iOS

##### <a name="possible-causes"></a>가능한 원인
일부 브라우저(예: Safari)에서는 동일한 디바이스에서 사용자 고유의 스트림을 가져오면 경합 상태를 실행하는 부작용이 발생합니다. 다른 디바이스에서 스트림을 가져오면 USB/IO 대역폭이 부족해질 수 있으며 sourceUnavailableError 비율이 급증할 수 있습니다.  


## <a name="azure-communication-services-call-automation-apis"></a>Azure Communication Services Call Automation API

다음은 Azure Communication Services Call Automation API에서 알려진 문제입니다.

- 현재 서버 애플리케이션에 지원되는 유일한 인증은 연결 문자열을 사용하는 것입니다.

- 호출은 동일한 Azure Communication Service 리소스의 엔터티 간에만 이루어져야 합니다. 리소스 간 통신이 차단됩니다.

- Teams 테넌트 사용자와 Azure Communication Service 사용자 또는 서버 애플리케이션 엔터티 간의 호출은 허용되지 않습니다.

- 애플리케이션에서 둘 이상의 PSTN ID로 전화를 건 다음 호출을 종료하면 다른 PSTN 엔터티 간의 호출이 삭제됩니다.

