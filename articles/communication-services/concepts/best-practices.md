---
title: Azure Communication Services - 모범 사례
description: Azure Communication Service 모범 사례에 대해 자세히 알아보기
author: srahaman
manager: akania
services: azure-communication-services
ms.author: srahaman
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d2c91ca300c626bc50b915098853f0f9e7cd2d2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073304"
---
# <a name="best-practices-azure-communication-services-calling-sdks"></a>모범 사례: Azure Communication Services 통화 SDK
이 문서에서는 SDK를 호출하는 ACS(Azure Communication Services)와 관련된 모범 사례에 대한 정보를 제공합니다.

## <a name="acs-web-javascript-sdk-best-practices"></a>ACS 웹 JavaScript SDK 모범 사례
이 섹션에서는 Azure Communication Services JavaScript 음성 및 비디오 통화 SDK와 관련된 모범 사례에 대한 정보를 제공합니다.

## <a name="javascript-voice-and-video-calling-sdk"></a>JavaScript 음성 및 영상 통화 SDK

### <a name="plug-in-microphone-or-enable-microphone-from-device-manager-when-acs-call-in-progress"></a>플러그 인 마이크 또는 ACS 통화가 진행 중일 때 디바이스 관리자에서 마이크 사용
통화 시작 시 사용 가능한 마이크가 없는 경우 마이크를 사용할 수 있게 되면 "noMicrophoneDevicesEnumerated" 통화 진단 이벤트가 발생합니다.
이 경우 애플리케이션은 `askDevicePermission`을 호출하여 디바이스 열거에 대한 사용자 동의를 얻어야 합니다. 그런 다음 사용자는 마이크를 음소거/음소거 해제할 수 있습니다.

### <a name="dispose-video-stream-renderer-view"></a>동영상 스트리밍 렌더러 삭제 보기
Communication Services 애플리케이션은 더 이상 필요하지 않을 때 `VideoStreamRendererView` 또는 상위 `VideoStreamRenderer` 인스턴스를 삭제해야 합니다.

### <a name="hang-up-the-call-on-onbeforeunload-event"></a>onbeforunload 이벤트에서 통화 중단
애플리케이션은 `onbeforeunload` 이벤트가 발생할 때 `call.hangup`을 호출해야 합니다.

### <a name="handling-multiple-calls-on-multiple-tabs-on-mobile"></a>모바일에서 여러 탭에 대 한 여러 호출 처리
응용 프로그램은 장치에서 마이크 및 카메라에 대 한 리소스 할당으로 인해 정의 되지 않은 동작이 발생할 수 있으므로 동시에 여러 브라우저 탭에서 호출에 연결 해서는 안 됩니다. 개발자는 새 작업을 시작 하기 전에 백그라운드에서 완료 될 때 항상 호출을 중지 하는 것이 좋습니다.

### <a name="handle-os-muting-call-when-phone-call-comes-in"></a>전화 통화가 들어오면 OS 음소거 통화를 처리 합니다.
전화 통화가 제공 되거나 음성 길잡이가 활성화 된 경우 ACS 호출 (iOS 및 Android 모두)에서, OS는 사용자 마이크와 카메라를 자동으로 음소거 합니다. Android에서 호출은 전화 통화가 끝난 후 자동으로 unmutes 하 고 비디오를 다시 시작 합니다. IOS에서 사용자 작업은 "음소거" 및 "비디오 시작"을 다시 수행 해야 합니다. 의 품질 이벤트를 사용 하 여 마이크가 예기치 않게 음소거 되었다는 알림을 수신할 수 있습니다 `microphoneMuteUnexpectedly` . 호출에 올바르게 다시 참가할 수 있도록 하려면 SDK 1.2.3-beta. 1 이상을 사용 해야 합니다.

```javascript
const latestMediaDiagnostic = call.api(SDK.Features.Diagnostics).media.getLatest();
const isIosSafari = (getOS() === OSName.ios) && (getPlatformName() === BrowserName.safari);
if (isIosSafari && latestMediaDiagnostic.microphoneMuteUnexpectedly && latestMediaDiagnostic.microphoneMuteUnexpectedly.value) {
  // received a QualityEvent on iOS that the microphone was unexpectedly muted - notify user to unmute their microphone and to start their video stream
}
```

응용 프로그램은를 호출 `call.startVideo(localVideoStream);` 하 여 비디오 스트림을 시작 하 고를 사용 하 여 오디오를 음소거 해제 해야 합니다 `this.currentCall.unmute();` .

### <a name="device-management"></a>디바이스 관리
Azure Communication Services SDK를 사용하여 디바이스 및 미디어 작업을 관리할 수 있습니다.
- 애플리케이션은 SDK 외부에서 스트리밍을 획득하기 위해 `getUserMedia` 또는 `getDisplayMedia`와 같은 기본 브라우저 API를 사용해서는 안 됩니다. 그렇게 하면 Communication Services SDK를 통해 `DeviceManager` 또는 기타 디바이스 관리 API를 사용하기 전에 미디어 스트리밍을 수동으로 삭제해야 합니다.

### <a name="request-device-permissions"></a>디바이스 권한 요청
SDK를 사용하여 디바이스 권한을 요청할 수 있습니다.
- 애플리케이션은 `DeviceManager.askDevicePermission`을 사용하여 오디오 및/또는 동영상 디바이스에 대한 액세스를 요청해야 합니다.
- 사용자가 액세스를 거부하는 경우 `DeviceManager.askDevicePermission`은 페이지를 새로 고친 후에도 후속 통화에서 지정된 디바이스 유형(오디오 또는 동영상)에 대해 'false'를 반환합니다. 이 시나리오에서 애플리케이션은 사용자가 이전에 액세스를 거부했음을 감지하고 사용자에게 수동으로 재설정하거나 지정된 디바이스 유형에 대한 액세스 권한을 명시적으로 부여하도록 요청해야 합니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 문서를 참조하세요.

- [앱에 채팅 추가](../quickstarts/chat/get-started.md)
- [앱에 음성 통화 추가](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [참조 설명서](reference.md)
