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
ms.openlocfilehash: 611f0080cf6db2af14e18b3049ca547a7cf49678
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133286912"
---
# <a name="known-issues-in-the-sdks-and-apis"></a>Sdk 및 Api의 알려진 문제

이 문서에서는 Sdk 및 통신 서비스 호출 자동화 Api를 호출 하는 Azure Communication Services와 관련 된 제한 사항 및 알려진 문제에 대 한 정보를 제공 합니다.

> [!IMPORTANT]
> 통화 환경의 품질에 영향을 줄 수 있는 여러 요인이 있습니다. 통신 서비스 네트워크 구성 및 테스트 모범 사례에 대해 자세히 알아보려면 [네트워크 권장 사항](./voice-video-calling/network-requirements.md)을 참조 하세요.

## <a name="javascript-sdk"></a>JavaScript SDK

다음 섹션에서는 통신 서비스 JavaScript 음성 및 비디오 호출 Sdk와 관련 된 알려진 문제에 대 한 정보를 제공 합니다.

### <a name="ios-with-safari-crashes-and-refreshes-the-page-if-a-user-tries-to-send-video-in-a-call"></a>사용자가 통화 시 비디오를 보내려고 하면 Safari를 사용 하는 iOS가 충돌 하 고 페이지를 새로 고칩니다.

iOS 15.1에는 Safari와 함께 iOS에 배치 되는 비디오를 사용 하는 대부분의 통신 서비스 호출에 영향을 주는 버그가 도입 되었습니다. 특히, 비디오가 설정 된 브라우저에서 iOS 15.1의 통신 서비스를 사용 하 여 사용자가 통신 서비스 호출 또는 모임 Microsoft Teams에 연결 하면 문제가 발생 합니다. 이러한 상황 집합으로 인해 Safari 브라우저가 충돌 합니다.

이 버그를 우회 하기 위해 관리 하 고 이제 사용자가 iOS 15.1의 비디오를 사용 하 여 통화를 조인할 수 있지만 Webkit의 다른 문제를 확인할 수 있습니다.

* 받는 사람의 끝 방향이 올바르지 않습니다. 완화: 장치 방향을 가로로 전환 합니다.
* 배경으로 이동 하면 사용자의 호출이 새로 고쳐집니다. 완화: 배경으로 이동 하기 전에 비디오를 중지 합니다.

이는 [Safari를 사용 하는 iOS 15.1에 대 한 알려진 버그](https://bugs.webkit.org/show_bug.cgi?id=231505)입니다.

### <a name="android-12-causes-video-artifacts-when-user-is-sending-video-in-a-call"></a>Android 12를 사용 하면 사용자가 통화 시 비디오를 보낼 때 비디오 아티팩트가 발생 합니다.

Android 12는 모든 Chromium 기반 브라우저에서 나가는 비디오에 영향을 주는 버그를 도입 했습니다. 특히, 비디오가 켜진 상태에서 Android 12의 통신 서비스를 사용 하 여 사용자가 전화를 연결 하면 문제가 발생 합니다. 비디오는 수신기의 끝에서 아티팩트로 렌더링 됩니다. 

이는 [Android 12의 알려진 버그](https://bugs.chromium.org/p/chromium/issues/detail?id=1237677)입니다.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>페이지를 새로 고치면 통화에서 사용자가 즉시 제거되지 않습니다.

사용자가 통화에 있고 페이지를 새로 고치도록 결정한 경우 Communication Services 미디어 서비스는 통화에서 이 사용자를 즉시 제거하지 않습니다. 사용자가 다시 조인할 때까지 기다립니다. 미디어 서비스 시간이 초과된 후에 사용자가 통화에서 제거됩니다.

통화하는 동안 최종 사용자가 애플리케이션의 페이지를 새로 고칠 필요가 없는 사용자 환경을 빌드하는 것이 가장 좋습니다. 사용자가 페이지를 새로 고치면 해당 사용자가 응용 프로그램으로 다시 반환 된 후 동일한 통신 서비스 사용자 ID를 다시 사용 합니다. 동일한 사용자 ID를 사용 하 여 다시 가입 하기 사용자는 컬렉션에서 동일한 기존 개체로 표시 됩니다 `remoteParticipants` . 호출에서 다른 참가자의 관점에서 보면 페이지를 새로 고치는 데 걸리는 시간 (최대 1 ~ 2 분) 동안 사용자가 호출에 남아 있습니다.

사용자가 새로 고침을 수행하기 전에 비디오를 전송한 경우 `videoStreams` 컬렉션은 서비스가 시간 초과되고 제거될 때까지 이전 스트림 정보를 유지합니다. 이 시나리오에서 응용 프로그램은 컬렉션에 추가 된 새 스트림을 관찰 하 고 가장 높은 스트림을 렌더링 하도록 결정할 수 있습니다 `id` . 

### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>웹의 여러 디바이스에서 여러 미리 보기를 렌더링하는 것은 불가능합니다.

이 문제는 알려진 제한 사항입니다. 자세한 내용은 [SDK 호출 개요](./voice-video-calling/calling-sdk-features.md)를 참조 하세요.

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>애플리케이션이 iOS 또는 iPadOS에서 실행되는 경우 Safari에서 디바이스를 열거할 수 없습니다.

응용 프로그램은 Safari iOS 또는 iPadOS에서 마이크 또는 스피커 장치 (예: Bluetooth)를 열거 하거나 선택할 수 없습니다. 이러한 운영 체제의 알려진 제한 사항입니다.

MacOS에서 Safari를 사용 하는 경우 앱은 통신 서비스 장치 관리자를 통해 스피커를 열거 하거나 선택할 수 없습니다. 이 시나리오에서는 운영 체제를 통해 장치를 선택 해야 합니다. MacOS에서 Chrome을 사용 하는 경우 앱은 통신 서비스 장치 관리자를 통해 장치를 열거 하거나 선택할 수 있습니다.

### <a name="device-mutes-and-incoming-video-stops-rendering-when-certain-interruptions-occur"></a>특정 중단이 발생 하면 장치 mutes 및 들어오는 비디오가 렌더링을 중지 합니다.

다른 응용 프로그램이 나 운영 체제에서 마이크 또는 카메라의 제어를 사용 하는 경우이 문제가 발생할 수 있습니다. 사용자가를 호출 하는 동안 발생할 수 있는 몇 가지 예는 다음과 같습니다.

- 들어오는 호출은 PSTN (공용 전환 전화 네트워크)을 통해 도착 하 고 마이크 장치 액세스를 캡처합니다.
- 예를 들어 사용자가 YouTube 비디오를 재생 하거나 FaceTime 호출을 시작 합니다. 다른 네이티브 응용 프로그램으로 전환 하면 마이크 또는 카메라에 대 한 액세스를 캡처할 수 있습니다.
- 사용자는 Siri를 사용 하도록 설정 하 여 마이크에 대 한 액세스를 캡처합니다.

이러한 모든 경우를 복구 하려면 사용자가 응용 프로그램으로 돌아가서 음소거를 해제 해야 합니다. 비디오의 경우에는 사용자가 비디오를 시작 하 여 중단 후 오디오 및 비디오를 시작 해야 합니다.

경우에 따라 마이크 또는 카메라 장치는 시간에 릴리스되지 않으며 원래 호출에서 문제를 일으킬 수 있습니다. 예를 들어 사용자가 YouTube 비디오를 시청 하는 동안 음소거를 해제 하려고 하거나 PSTN 호출이 동시에 활성화 되는 경우입니다. 

이 문제가 발생 하는 환경은 다음과 같습니다.

- 클라이언트 라이브러리: 통화(JavaScript)
- 브라우저: Safari
- 운영 체제: iOS

### <a name="repeatedly-switching-video-devices-might-cause-video-streaming-to-stop-temporarily"></a>비디오 장치를 반복적으로 전환 하면 비디오 스트리밍이 일시적으로 중지 될 수 있습니다.

비디오 장치 간을 전환 하면 선택한 장치에서 스트림을 획득 하는 동안 비디오 스트림이 일시 중지 될 수 있습니다. 디바이스 간을 자주 전환하면 성능이 저하될 수 있습니다. 개발자는 다른 장치 스트림을 시작 하기 전에 한 장치 스트림을 중지 하는 것이 가장 좋습니다.

### <a name="bluetooth-headset-microphone-isnt-detected-or-audible-during-the-call-on-safari-on-ios"></a>iOS에서 Safari를 호출 하는 동안 Bluetooth 헤드셋 마이크가 검색 되지 않거나

Bluetooth 헤드셋은 iOS의 Safari에서 지원되지 않습니다. Bluetooth 장치는 사용 가능한 마이크 옵션에 나열 되지 않으며 Safari를 통해 Bluetooth를 사용 하는 경우 다른 참가자는이를 듣지 못합니다.

이는 알려진 운영 체제 제한 사항입니다. MacOS 및 iOS/iPadOS에서 Safari를 사용 하는 경우 통신 서비스 장치 관리자를 통해 스피커 장치를 열거 하거나 선택할 수 없습니다. 이는 Safari에서 스피커의 열거 나 선택을 지원 하지 않기 때문입니다. 이 시나리오에서는 운영 체제를 사용 하 여 장치 선택을 업데이트 합니다.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>디바이스의 회전으로 비디오 품질이 떨어질 수 있습니다.

사용자가 장치를 회전할 때이 이동으로 인해 스트리밍이 되는 비디오의 품질이 저하 될 수 있습니다.

이 문제가 발생 하는 환경은 다음과 같습니다.

- 영향을 받는 디바이스: Google Pixel 5, Google Pixel 3a, Apple iPad 8 및 Apple iPad X
- 클라이언트 라이브러리: 통화(JavaScript)
- 브라우저: Safari, Chrome
- 운영 체제: iOS, Android

### <a name="camera-switching-makes-the-screen-freeze"></a>카메라 전환으로 인해 화면이 동결됩니다. 

통신 서비스 사용자가 JavaScript 호출 SDK를 사용 하 여 호출을 조인한 후 카메라 스위치 단추를 선택 하면 UI가 응답 하지 않을 수 있습니다. 그런 다음 사용자는 응용 프로그램을 새로 고치거 나 브라우저를 배경으로 푸시 해야 합니다.

이 문제가 발생 하는 환경은 다음과 같습니다.

- 영향을 받는 디바이스: Google Pixel 4a
- 클라이언트 라이브러리: 통화(JavaScript)
- 브라우저: Chrome
- 운영 체제: iOS, Android

### <a name="video-signal-problem-when-the-call-is-in-connecting-state"></a>호출이 연결 상태인 경우 비디오 신호 문제 

호출이 *연결* 상태에 있는 동안 사용자가 신속 하 게 비디오를 켜고 끌 경우 호출에 대해 획득 한 스트림에 문제가 발생할 수 있습니다. 호출이 *연결* 상태에 있는 동안 비디오를 켜고 끄도록 요구 하지 않는 방식으로 개발자가 앱을 빌드하는 것이 가장 좋습니다. 성능 저하는 다음과 같은 시나리오에서 발생할 수 있습니다.

 - 사용자가 오디오로 시작 하 고 비디오를 시작 및 중지 한 다음 호출이 *연결* 상태에 있는 경우
 - 사용자가 오디오로 시작 하 고 비디오를 시작 및 중지 하는 반면, 호출은 *로비* 상태에 있습니다.

### <a name="enumerating-or-accessing-devices-for-safari-on-macos-and-ios"></a>MacOS 및 iOS에서 Safari 용 장치 열거 또는 액세스 

특정 환경에서는 일정 시간 후에 장치 권한이 다시 설정 되는 것을 알 수 있습니다. MacOS 및 iOS에서 Safari는 스트림을 획득 하지 않는 한 오랜 시간 동안 권한을 유지 하지 않습니다. 이 문제를 해결 하는 가장 간단한 방법은 `DeviceManager.askDevicePermission()` 장치 관리자의 장치 열거 api를 호출 하기 전에 api를 호출 하는 것입니다. 이러한 열거형 Api에는, 및가 포함 됩니다 `DeviceManager.getCameras()` `DeviceManager.getSpeakers()` `DeviceManager.getMicrophones()` . 권한이 있으면 사용자에 게 아무것도 표시 되지 않습니다. 권한이 없으면 사용자에 게 사용 권한을 다시 요청 하는 메시지가 표시 됩니다.

이 문제가 발생 하는 환경은 다음과 같습니다.

- 영향을 받는 장치: iPhone
- 클라이언트 라이브러리: 통화(JavaScript)
- 브라우저: Safari
- 운영 체제: iOS

### <a name="delay-in-rendering-remote-participant-videos"></a>원격 참가자 비디오 렌더링 지연

진행 중인 그룹 통화 중에 _사용자 A가_ 비디오를 보낸 다음 _사용자 B가_ 통화에 참여한다고 가정합니다. 사용자 B에게 사용자 A의 비디오가 표시되지 않거나 사용자 A의 비디오가 긴 지연 후 렌더링을 시작하는 경우가 있습니다. 네트워크 환경 구성 문제로 인해 이 지연이 발생할 수 있습니다. 자세한 내용은 [네트워크 권장 사항을 참조하세요.](./voice-video-calling/network-requirements.md)

### <a name="using-third-party-libraries-during-the-call-might-result-in-audio-loss"></a>호출 중에 타사 라이브러리를 사용하면 오디오가 손실될 수 있습니다.

애플리케이션 내에서 별도로 사용하는 경우 `getUserMedia` 오디오 스트림이 손실됩니다. 이는 타사 라이브러리가 Azure Communication Services 라이브러리에서 디바이스 액세스를 인수하기 때문입니다.

- `getUserMedia`호출하는 동안 내부적으로 API를 사용하는 타사 라이브러리를 사용하지 마세요.
- 타사 라이브러리를 계속 사용해야 하는 경우 오디오 스트림을 복구하는 유일한 방법은 선택한 디바이스를 변경하거나(사용자가 두 개 이상 있는 경우) 통화를 다시 시작하는 것입니다.

이 문제가 발생하는 환경은 다음과 같습니다.

- 브라우저: Safari
- 운영 체제: iOS

이 문제의 원인은 동일한 디바이스에서 사용자 고유의 스트림을 획득하면 경합 상태가 발생할 경우 부작용이 발생할 수 있습니다. 다른 디바이스에서 스트림을 획득하면 사용자가 USB/IO 대역폭이 부족해질 수 있으며 `sourceUnavailableError` 속도가 더 높아질 수 있습니다.  

### <a name="support-for-simulcast"></a>simulcast 지원

Simulcast는 클라이언트가 동일한 비디오 스트림을 서로 다른 해상도 및 비트 전송률로 두 번 인코딩하는 기술입니다. 그러면 클라이언트에서 Communication Services 수신할 스트림을 결정할 수 있습니다. Windows, Android 또는 iOS용 Communication Services 호출 라이브러리 SDK는 동시 스트림 전송을 지원합니다. Communication Services 웹 SDK는 현재 simulcast 스트림 전송을 지원하지 않습니다.

## <a name="communication-services-call-automation-apis"></a>Automation API Communication Services 호출

다음은 Communication Services Call Automation API의 알려진 문제입니다.

- 서버 애플리케이션에 대해 현재 지원되는 유일한 인증은 연결 문자열을 사용하는 것입니다.

- 동일한 Communication Services 리소스의 엔터티 간에만 호출합니다. 리소스 간 통신이 차단됩니다.

- Microsoft Teams 테넌트 사용자와 Communication Services 사용자 또는 서버 애플리케이션 엔터티 간의 호출은 허용되지 않습니다.

- 애플리케이션이 둘 이상의 PSTN ID로 전화를 거는 경우 호출을 종료하면 다른 PSTN 엔터티 간의 호출이 삭제됩니다.

