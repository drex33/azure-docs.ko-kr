---
title: Azure Communication Services 통화 SDK 개요
titleSuffix: An Azure Communication Services concept document
description: 통화 SDK에 대한 개요를 제공합니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 7069d5089142f4fc5a6fff6492726b6f949c8f18
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259012"
---
# <a name="calling-sdk-overview"></a>통화 SDK 개요

[!INCLUDE [SDP Plan B Deprecation Notice](../../includes/plan-b-sdp-deprecation.md)]

Calling SDK를 사용하면 최종 사용자 디바이스에서 음성 및 비디오 통신 환경을 구동할 수 있습니다. 이 페이지에서는 플랫폼 및 브라우저 지원 정보를 비롯한 통화 기능에 대해 자세히 설명합니다. 지금 바로 시작하려면 [통화 빠른 시작](../../quickstarts/voice-video-calling/getting-started-with-calling.md) 또는 [통화 주인공 샘플](../../samples/calling-hero-sample.md)을 확인하세요. 

개발을 시작한 후에는 [알려진 문제 페이지](../known-issues.md)를 확인하여 작업 중인 버그를 찾을 수 있습니다.

Calling SDK의 주요 기능:

- **주소 지정** - Azure Communication Services는 통신 엔드포인트의 주소를 지정하는 데 사용되는 일반 [ID](../identity-model.md)를 제공합니다. 클라이언트는 이러한 ID를 사용하여 서비스에 대해 인증하고 서로 통신합니다. 이러한 ID는 통화에 연결된 사용자를 클라이언트가 볼 수 있도록 하는 통화 API를 호출하는 데 사용됩니다(명단).
- **암호화** - Calling SDK는 트래픽을 암호화하고 네트워크 변조를 방지합니다. 
- **디바이스 관리 및 미디어** - Calling SDK는 오디오 및 비디오 디바이스에 바인딩하는 기능을 제공하고, 통신 데이터 평면에서의 효율적인 전송을 위해 콘텐츠를 인코딩하며, 지정한 출력 디바이스 및 보기로 콘텐츠를 렌더링합니다. API는 화면 및 애플리케이션 공유에도 제공됩니다.
- **PSTN** - Calling SDK는 [Azure Portal에서 얻은 전화 번호를 사용하거나](../../quickstarts/telephony-sms/get-phone-number.md) 프로그래밍 방식으로 기존에 공개적으로 전환된 전화 시스템을 통해 음성 통화를 수신하고 시작할 수 있습니다.
- **Teams 미팅** - Calling SDK는 [Teams 미팅에 조인](../../quickstarts/voice-video-calling/get-started-teams-interop.md)하고 Teams 음성 및 비디오 평면과 상호 작용할 수 있습니다. 
- **알림** - Calling SDK는 클라이언트가 들어오는 호출에 대한 알림을 받을 수 있도록 하는 API를 제공합니다. 앱이 포그라운드에서 실행되지 않는 상황에서, 패턴을 사용하여 최종 사용자에게 들어오는 호출을 알리기 위해 [팝업 알림](../notifications.md)(“알림”)을 실행할 수 있습니다. 

## <a name="detailed-capabilities"></a>상세 기능 

다음 목록에서는 현재 Azure Communication Services 통화 SDK에서 사용할 수 있는 기능 세트를 보여줍니다.


| 기능 그룹 | 기능                                                                                                          | JS  | Windows | Java(Android) | Objective-C(iOS) |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ------- | -------------- | ----------------- |
| 핵심 기능 | 두 사용자 간에 일 대 일 호출 배치                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 세 명 이상의 사용자를 포함하는 그룹 통화(최대 350 사용자) 배치                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 세 명 이상의 사용자가 있는 그룹 호출로 일 대 일 호출 승격                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 시작된 후 그룹 호출 조인                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 진행 중인 그룹 호출에 참가하기 위해 다른 VoIP 참가자 초대                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
| 중간 호출 컨트롤  | 비디오 켜기/끄기                                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 마이크 음소거/음소거 해제                                                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 카메라 간 전환                                                                                              | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 로컬 보류/유지 취소                                                                                                  | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 활성 스피커                                                                                                      | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 호출용 스피커 선택                                                                                            | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 통화에 대한 마이크 선택                                                                                         | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 참가자의 상태 표시<br/>*유휴, 초기 미디어, 연결, 연결됨, 보류 중, 로비 내, 연결 끊김*         | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 호출 상태 표시<br/>*초기 미디어, 수신, 연결 중, 울림, 연결됨, 보류 중, 연결 끊기, 연결 끊어짐* | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 참가자가 음소거되어 있는지 여부 표시                                                                                      | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 참가자가 전화를 끊은 이유 표시                                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
| 화면 공유    | 애플리케이션 내에서 전체 화면 공유                                                                 | ✔️   | ❌       | ❌              | ❌                 |
|                   | 특정 애플리케이션 공유(실행 중인 애플리케이션 목록에서)                                                | ✔️   | ❌       | ❌              | ❌                 |
|                   | 열려 있는 탭 목록에서 웹 브라우저 탭 공유                                                                  | ✔️   | ❌       | ❌              | ❌                 |
|                   | 참가자가 원격 화면 공유를 볼 수 있음                                                                            | ✔️   | ✔️       | ✔️              | ✔️                 |
| 명단            | 참가자 리스트                                                                                                   | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 참가자 제거                                                                                                | ✔️   | ✔️       | ✔️              | ✔️                 |
| PSTN              | PSTN 참가자를 사용하여 일 대 일 호출 배치                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | PSTN 참가자를 사용하여 그룹 호출 배치                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | PSTN 참가자를 사용하여 일 대 일 호출을 그룹 호출로 승격                                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 그룹 호출에서 PSTN 참가자로 전화 걸기                                                                    | ✔️   | ✔️       | ✔️              | ✔️                 |
| 일반           | 오디오 테스트 서비스를 사용하여 마이크, 스피커 및 카메라 테스트(8:echo123을 호출하여 사용 가능)                   | ✔️   | ✔️       | ✔️              | ✔️                 |
| 디바이스 관리 | 오디오 및/또는 비디오 사용 권한 요청                                                                       | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 카메라 목록 가져오기                                                                                                     | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 카메라 설정                                                                                                          | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 선택한 카메라 가져오기                                                                                                 | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 마이크 목록 가져오기                                                                                                 | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 마이크 설정                                                                                                      | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 선택한 마이크 가져오기                                                                                             | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 스피커 목록 가져오기                                                                                                   | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 스피커 설정                                                                                                         | ✔️   | ✔️       | ❌              | ❌                 |
|                   | 선택한 스피커 가져오기                                                                                                | ✔️   | ✔️       | ❌              | ❌                 |
| 비디오 렌더링   | 여러 위치에서 단일 비디오 렌더링(로컬 카메라 또는 원격 스트림)                                                  | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 크기 조정 모드 설정/업데이트                                                                                           | ✔️   | ✔️       | ✔️              | ✔️                 |
|                   | 원격 비디오 스트림 렌더링                                                                                          | ✔️   | ✔️       | ✔️              | ✔️                 |


## <a name="calling-sdk-streaming-support"></a>통화 SDK 스트리밍 지원
Communication Services 통화 SDK는 다음과 같은 스트리밍 구성을 지원합니다.

| 제한                                                         | 웹                         | Windows/Android/iOS        |
| ------------------------------------------------------------- | --------------------------- | -------------------------- |
| **# 동시에 보낼 수 있는 나가는 스트림 수**     | 비디오 1개 또는 화면 공유 1개 | 비디오 1개 + 화면 공유 1개 |
| **# 동시에 렌더링할 수 있는 들어오는 스트림 수** | 비디오 1개 또는 화면 공유 1개 | 비디오 6개 + 화면 공유 1개 |

통화 SDK는 이러한 제한을 적용하지 않지만 초과할 경우 사용자에게 성능 저하가 발생할 수 있습니다.

## <a name="calling-sdk-timeouts"></a>통화 SDK 제한 시간

Communication Services 통화 SDK에는 다음 제한 시간이 적용됩니다.

| 작업                                                                      | 제한 시간(초) |
| --------------------------------------------------------------------------- | ------------------ |
| 참가자 다시 연결/제거                                               | 120                |
| 통화에서 새 형식을 추가하거나 제거합니다(비디오 또는 화면 공유 시작/중지). | 40                 |
| 통화 전송 작업 제한 시간                                             | 60                 |
| 1:1 통화 설정 제한 시간                                              | 85                 |
| 그룹 통화 설정 제한 시간                                            | 85                 |
| PSTN 통화 설정 제한 시간                                             | 115                |
| 1:1 통화를 그룹 통화 제한 시간으로 승격                                    | 115                |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>OS 및 브라우저에서 JavaScript 통화 SDK 지원

다음 표에서는 현재 사용 가능한 지원되는 브라우저 세트를 나타냅니다. 달리 명시하지 않는 한 **가장 최근의 세 가지 버전의 브라우저가 지원됩니다**.

| 플랫폼     | Chrome | Safari | Edge(Chromium)  |
| ------------ | ------ | ------ | --------------   |
| Android      | ✔️      | ❌      | ❌           | 
| iOS          | ❌      | ✔️      | ❌           |
| macOS        | ✔️      | ✔️      | ❌           | 
| Windows      | ✔️      | ❌      | ✔️           |
| Ubuntu/Linux | ✔️      | ❌      | ❌           |    

* Safari에서는 1:1 통화가 지원되지 않습니다.
* iOS 또는 Android에서는 발신 화면 공유가 지원되지 않습니다.
* [Safari의 iOS 앱은 마이크 및 스피커 디바이스(예: Bluetooth)를 열거/선택할 수 없습니다](../known-issues.md#enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados). 이는 OS의 제한 사항이며, 항상 하나의 디바이스만 있고, OS가 기본 디바이스 선택을 제어합니다.

## <a name="android-calling-sdk-support"></a>Android Calling SDK 지원

* Android API 수준 21 이상 지원

* Java 7 이상 지원

* Android Studio 2.0 지원

## <a name="ios-calling-sdk-support"></a>iOS Calling SDK 지원

* 빌드 시 iOS 10.0 이상 및 런타임 시 iOS 12.0 이상 지원

* Xcode 12.0 이상

## <a name="calling-client---browser-security-model"></a>클라이언트 - 브라우저 보안 모델 호출

### <a name="user-webrtc-over-https"></a>HTTPS를 통한 사용자 WebRTC

`getUserMedia`와 같은 WebRTC API에서는 이러한 API를 호출하는 앱이 HTTPS를 통해 제공되어야 합니다.

로컬 개발의 경우 `http://localhost`를 사용할 수 있습니다.

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Communication Service 호출 SDK를 iframe에 포함

다양한 브라우저에서 새 [권한 정책(기능 정책이라고도 함)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute)을 채택하고 있습니다. 이 정책은 애플리케이션이 원본 간 iframe 요소를 통해 디바이스의 카메라 및 마이크에 액세스할 수 있는 방법을 제어하여 호출 시나리오에 영향을 줍니다.

Iframe을 사용하여 다른 도메인에서 앱의 일부를 호스팅하려면 iframe에 올바른 값이 포함된 `allow` 특성을 추가해야 합니다.

예를 들어 이 iframe은 카메라와 마이크 액세스를 모두 허용합니다.

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [통화 시작](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

자세한 내용은 다음 항목을 참조하세요.
- 일반적인 [통화 흐름](../call-flows.md) 숙지
- [통화 형식](../voice-video-calling/about-call-types.md)에 대한 자세한 정보
- [PSTN 솔루션 계획](../telephony-sms/plan-solution.md)
