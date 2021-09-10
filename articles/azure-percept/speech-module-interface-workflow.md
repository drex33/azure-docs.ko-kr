---
title: Azure Percept 음성 모듈 인터페이스 워크플로
description: Azure Percept 음성 모듈에 사용할 수 있는 메서드 및 워크플로에 대해 설명합니다.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 7/19/2021
ms.custom: template-concept
ms.openlocfilehash: 7ac6a2e02dacb63ebe36d6c16c7a39e370751033
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566325"
---
# <a name="azure-percept-speech-module-interface-workflow"></a>Azure Percept 음성 모듈 인터페이스 워크플로

이 문서에서는 Azure Percept 음성 모듈이 IoT Hub와 상호 작용하는 방법을 설명합니다. 상호 작용은 모듈 쌍과 모듈 메서드를 통해 가능합니다. 또한 음성 모듈을 호출하는 데 사용되는 직접 메서드 호출을 나열합니다.

## <a name="speech-module-interaction-with-iot-hub-via-module-twin-and-module-method"></a>모듈 쌍 및 모듈 메서드를 통한 IoT 허브와 음성 모듈의 상호 작용
- IoT Hub는 모듈 쌍을 사용하여 음성 모듈 설정을 배포하고 설정은 속성에 저장됩니다. 음성 모듈은 모듈 쌍이 보고한 속성을 통해 디바이스 정보 및 원격 분석을 IoT 허브에 업데이트할 수 있습니다.
- IoT Hub는 모듈 메서드를 통해 음성 모듈에 제어 요청을 보낼 수 있습니다.
- IoT Hub는 모듈 메서드를 통해 음성 모듈 상태를 가져올 수 있습니다.

자세한 내용은 [IoT Hub의 모듈 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-module-twins.md)을 참조하세요.


## <a name="speech-module-states"></a>음성 모듈 상태
- **IoTInitialized**: IoT 모듈이 초기화되고 음성 모듈과 에지 허브 모듈 간의 네트워크가 연결되었음을 나타냅니다.
- **Authenticating**: Azure Audio 디바이스 인증이 처리 중입니다.
- **Authenticated**: Azure Audio 디바이스 인증이 완료되었습니다. 실패한 경우 IoT 허브에 오류 메시지가 표시됩니다.
- **MicDiscovering**: ALSA 인터페이스를 통해 마이크 배열을 열거하기 시작합니다.
- **MicDiscovered**: 마이크 배열 열거가 완료되었습니다. 실패한 경우 IoT 허브에 오류 메시지가 표시됩니다.
- **SpeechConfigured**: CC 구성이 완료되었습니다. 실패한 경우 IoT 허브에 오류 메시지가 표시됩니다.
- **SpeechStarted**: 봇이 구성되어 실행 중임을 나타냅니다.
- **SpeechStopped**: 봇이 중지되었음을 나타냅니다.
- **DeviceRemoved**: Azure Audio 디바이스가 제거되었음을 나타냅니다.


## <a name="speech-bot-states"></a>음성 봇 상태
음성 봇 상태 쿼리는 **SpeechStarted** 음성 모듈 상태에서만 지원됩니다.
- **준비**: KWS가 준비되어 음성 활성화를 대기하는 중입니다.
- **듣는 중**: 봇이 음성 입력을 수신 대기하는 중입니다.
- **생각 중**: 봇이 응답을 대기하는 중입니다.
- **말하는 중**: 봇이 응답을 받고 응답을 말하는 중입니다.

## <a name="interaction-between-iot-hub-and-the-speech-module"></a>IoT Hub와 음성 모듈 간의 상호 작용 
이 섹션에서는 IoT Hub가 음성 모듈과 상호 작용하는 방법을 설명합니다. 다이어그램에 표시된 것처럼 세 가지 유형의 메시지가 있습니다.
- 필요한 속성이 있는 배포 및 보고된 속성으로 업데이트
- 모듈 메서드 호출
- 원격 분석 업데이트

:::image type="content" source="media/speech-module-interface-workflow/speech-module-diagram.png" alt-text="IoT Hub와 음성 모듈 간의 상호 작용을 보여 주는 다이어그램":::

IoT Hub는 두 개의 매개 변수를 사용하여 모듈 메서드를 호출합니다.
- 모듈 메서드 이름(대/소문자 구분)
- 메서드 페이로드

음성 모듈은 다음으로 응답합니다.
- 상태 코드
    - **0** = 유휴 상태
    - **102** = 처리 중
    - **200** = 성공
    - **202** = 보류 중
    - **500** = 실패
    - **501** = 없음
- 상태 페이로드

다음은 모듈 메서드 GetModuleState를 사용하는 예제입니다.
1. 다음 매개 변수를 사용하여 메서드를 호출합니다.
    - 문자열: “GetModuleState”
    - Unspecified
1. 응답:
    - 상태 코드: 200
    - 페이로드: “DeviceRemoved”

## <a name="next-steps"></a>다음 단계
[Azure IoT Hub를 사용하여 음성 도우미 애플리케이션을 구성](./how-to-configure-voice-assistant.md)할 때 이러한 개념을 적용해 보세요.