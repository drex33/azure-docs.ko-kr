---
title: Azure Communication Services 호출 진단
titleSuffix: An Azure Communication Services concept document
description: 호출 진단 기능에 대한 개요를 제공합니다.
author: probableprime
ms.author: rifox
manager: chpalm
services: azure-communication-services
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 23b75ff29e32bafdce320d334d9236902cbe4b2c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667614"
---
# <a name="call-diagnostics"></a>통화 진단

Azure Communication Services 통화 작업을 할 때 고객에게 문제를 일으키는 문제 또는 문제가 발생할 수 있습니다. 이를 돕기 위해 호출의 다양한 속성을 검사하여 문제가 무엇인지 확인할 수 있는 "진단 호출"이라는 기능이 있습니다.

**호출 진단은 현재 JS/웹 SDK에서만 지원됩니다.**

## <a name="accessing-diagnostics"></a>진단에 액세스

통화 진단은 핵심 `Call` API의 확장된 기능으로, 활성 호출을 진단할 수 있도록 합니다.

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

## <a name="diagnostic-values"></a>진단 값

다음과 같은 사용자 연결 진단을 사용할 수 있습니다.

### <a name="network-values"></a>네트워크 값

| 이름                      | Description                                                     | 가능한 값                                                                                                                                                                                                                                  | 사용 사례                                           |
| ------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| noNetwork                 | 사용 가능한 업데이트가 없습니다.                                  | - 사용 가능한 네트워크가 없어서 호출을 시작하지 못한 경우 `True`로 설정됩니다. <br/> - ICE 후보가 있는 경우 `False`로 설정됩니다.                                                                                                  | 디바이스가 네트워크에 연결되지 않았습니다.               |
| networkRelaysNotReachable | 네트워크에 문제가 있습니다.                                        | - 네트워크에 ACS 릴레이에 연결하도록 허용하지 않는 일부 제약 조건이 있는 경우 `True`로 설정됩니다. <br/> - 새 호출 시 `False`로 설정됩니다.                                                                                                | 호출하는 동안 WiFi 신호가 켜지고 꺼질 때 |
| networkReconnect          | 연결이 끊어졌으며 네트워크에 다시 연결하고 있습니다. | - 네트워크 연결이 끊어지면 `Bad`로 설정됩니다. <br/> - `Poor` 미디어 전송 연결이 끊어지면 로 설정 <br/> - 새 세션이 연결되면 `Good`으로 설정됩니다.                                                                       | 낮은 대역폭, 인터넷 없음                          |
| networkReceiveQuality     | 들어오는 스트림 품질에 대한 표시기입니다.                 | - 스트림을 받는 데 심각한 문제가 있는 경우 `Bad`로 설정됩니다. 품질 <br/> - 스트림을 받는 데 경미한 문제가 있는 경우 `Poor`로 설정됩니다. 품질 <br/> - 스트림을 받는 데 문제가 없는 경우 `Good`으로 설정됩니다. | 낮은 대역폭                                       |

### <a name="audio-values"></a>오디오 값

| Name                           | Description                                                     | 가능한 값                                                                                                                                                                                                                                                                                                   | 사용 사례                                                        |
| ------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| noSpeakerDevicesEnumerated     | 사용자 시스템에 오디오 출력 디바이스(스피커)가 없습니다. | - 시스템에 스피커 디바이스가 없고 스피커 선택이 지원되는 경우 `True`로 설정됩니다. <br/> - 시스템에 스피커 디바이스가 1개 이상 없고 스피커 선택이 지원되는 경우 `False`로 설정됩니다.                                                                                             | 모든 스피커가 플러그가 뽑혀 있습니다.                                       |
| speakingWhileMicrophoneIsMuted | 음소거 상태에서 말합니다.                                   | - 로컬 마이크가 음소거되고 로컬 사용자가 말하는 경우 `True`로 설정됩니다. <br/> - 로컬 사용자가 말하기를 중지하거나 마이크를 음소거 해제할 때 `False`로 설정됩니다. <br/> \* 참고: 현재 오디오 수준 샘플은 webrtc에서 가져온 대로 safari에서 아직 지원되지 않습니다. 않습니다.                 | 통화하는 동안 마이크를 음소거하고 마이크에 대고 말합니다.           |
| noMicrophoneDevicesEnumerated  | 사용자 시스템에 오디오 캡처 디바이스(마이크)가 없습니다.      | - 시스템에 마이크 디바이스가 없는 경우 `True`로 설정됩니다. <br/> - 시스템에 마이크 디바이스가 1개 이상 있는 경우 `False`로 설정됩니다.                                                                                                                                                              | 모든 마이크는 통화 중에 연결이 끊어집니다.                   |
| microphoneNotFunctioning       | 마이크가 작동하지 않습니다.                                  | - 마이크 디바이스가 시스템에서 사용하지 않도록 설정되었거나 다른 프로세스에서 사용되고 있기 때문에 로컬 오디오 스트림 전송을 시작하지 못한 경우 `True`로 설정됩니다. 이 UFD가 발생하는 데 약 10초가 걸립니다. <br/> - 마이크가 오디오 스트림을 다시 성공적으로 보내기 시작하면 `False`로 설정됩니다. | 마이크를 사용할 수 없습니다. 시스템에서 마이크 액세스가 사용하지 않도록 설정되었습니다. |
| microphoneMuteUnexpectedly     | 마이크가 음소거되었습니다.                                             | - 마이크가 예기치 않게 음소거 상태가 되면 `True`로 설정됩니다. <br/> - 마이크가 오디오 스트림을 성공적으로 보내기 시작하면 `False`로 설정됩니다.                                                                                                                                                                  | 마이크가 시스템에서 음소거되었습니다.                             |
| microphonePermissionDenied     | 디바이스의 볼륨이 낮거나 macOS에서 거의 무음입니다. | - 시스템 설정(오디오)에서 오디오 사용 권한이 거부되면 `True`로 설정됩니다. <br/> - 스트림이 성공적으로 획득되면 `False`로 설정됩니다. <br/> 참고: 이 진단은 macOS에서만 작동합니다.                                                                                                                             | 설정에서 마이크 사용 권한이 사용하지 않도록 설정되었습니다.             |

### <a name="camera-values"></a>카메라 값

| 이름                   | Description                                            | 가능한 값                                                                                                                                                                                                                                                                                              | 사용 사례                                                                       |
| ---------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| cameraFreeze           | 카메라가 5초 넘게 프레임 생성을 중지합니다. | - 로컬 비디오 스트림이 중지된 경우 `True`로 설정합니다. 즉, 원격 쪽 사용자가 화면에서 중지된 비디오를 보거나 원격 참가자의 화면에서 비디오가 렌더링되지 않음을 의미합니다. <br/> - 중지가 끝나고 사용자가 정상적으로 비디오를 볼 수 있는 경우 `False`로 설정됩니다. | 통화 중에 카메라가 연결이 끊어지거나 네트워크에 문제가 생겨 카메라가 중지되었습니다. |
| cameraStartFailed      | 일반 카메라 오류입니다.                                | - 카메라 디바이스가 시스템에서 사용하지 않도록 설정되었거나 다른 프로세스에서 사용되고 있기 때문에 로컬 비디오 전송을 시작하지 못한 경우 `True`로 설정됩니다. <br/> - 선택한 카메라 디바이스가 로컬 비디오를 성공적으로 보낼 때 `False`로 설정됩니다. 를 다시 클릭합니다.                                                  | 카메라 오류                                                                 |
| cameraStartTimedOut    | 카메라가 잘못된 상태인 일반적인 시나리오입니다.          | - 카메라 디바이스가 비디오 스트림 전송을 시작하는 동안 시간이 초과될 경우 `True`로 설정됩니다. <br/> - 선택한 카메라 디바이스가 로컬 비디오를 다시 보낼 경우 `False`로 설정됩니다.                                                                                                                                         | 카메라 오류                                                                 |
| cameraPermissionDenied | 설정에서 카메라 사용 권한이 거부되었습니다.            | - 시스템 설정(비디오)에서 카메라 사용 권한이 거부되면 `True`로 설정됩니다. <br/> - 스트림이 성공적으로 획득되면 `False`로 설정됩니다. <br> 참고: 이 진단은 macOS Chrome에서만 작동합니다.                                                                                                                  | 설정에서 카메라 사용 권한이 사용하지 않도록 설정되었습니다.                                |

### <a name="misc-values"></a>기타 값

| 이름                         | Description                                                  | 가능한 값                                                                                                                                                                                         | 사용 사례                                 |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| screenshareRecordingDisabled | 설정의 기본 설정에 의해 시스템 화면 공유가 거부되었습니다. | - 시스템 설정(공유)에서 화면 공유 권한이 거부되면 `True`로 설정됩니다. <br/> - 스트림이 성공적으로 획득되면 `False`로 설정됩니다. <br/> 참고: 이 진단은 macOS.Chrome에서만 작동합니다. | 설정에서 화면 녹화가 사용하지 않도록 설정되었습니다. |

## <a name="diagnostic-events"></a>진단 이벤트

- 통화 진단이 변경될 때를 모니터링하기 위해 `diagnosticChanged` 이벤트를 구독합니다.

```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` +
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

## <a name="get-the-latest-diagnostics"></a>최신 진단 받기

- 발생한 최신 통화 진단 값을 가져옵니다. 진단이 정의되지 않은 경우 절대 발생하기 않기 때문입니다.

```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();

console.log(
  `noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`
);

console.log(
  `networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`
);

console.log(
  `networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`
);

const latestMediaDiagnostics = callDiagnostics.media.getLatest();

console.log(
  `speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`
);

console.log(
  `cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`
);

console.log(
  `microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`
);
```
