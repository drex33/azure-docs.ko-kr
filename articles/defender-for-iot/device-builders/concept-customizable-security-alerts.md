---
title: IoT Hub에 대한 사용자 지정 보안 경고
description: Defender for IoT Hub의 기능 및 서비스를 통해 사용자 지정 가능한 보안 경고와 권장되는 수정 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 3d72ee2d4cd91d0a922a7af61fbafd2cdb9a349a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319000"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Defender for IoT Hub 사용자 지정 보안 경고

Defender for IoT는 고급 분석과 위협 인텔리전스를 사용해 IoT 솔루션을 지속적으로 분석하여 악의적인 활동을 경고합니다.

예상되는 각 디바이스의 동작에 대한 지식을 기반으로 사용자 지정 경고를 만듭니다. 이러한 경고는 조직 배포 또는 환경의 잠재적 변경 내용을 가장 효율적으로 나타내는 지표로 작동합니다.

다음 Defender for IoT 경고 목록은 예상되는 IoT Hub 동작을 기반으로 사용자가 정의할 수 있습니다. 각 경고를 사용자 지정하는 방법에 대한 자세한 내용은 [사용자 지정 경고 만들기](quickstart-create-custom-alerts.md)를 참조하세요.

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>IoT Hub에서 기본 제공되는 사용자 지정 경고

### <a name="low-severity"></a>낮은 심각도

| 경고 이름 | 심각도 | 데이터 원본 | Description | 경고 유형 |
|--|--|--|--|--|
| 사용자 지정 경고 - AMQP 프로토콜의 클라우드-디바이스 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 클라우드-디바이스 메시지 수(AMQP 프로토콜)가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_AmqpC2DMessagesNotInAllowedRange |
| 사용자 지정 경고 - AMQP 프로토콜에서 거부된 클라우드-디바이스 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 디바이스에서 거부한 특정 시간 범위 내의 클라우드-디바이스 메시지 수(AMQP 프로토콜)가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_AmqpC2DRejectedMessagesNotInAllowedRange |
| 사용자 지정 경고 - AMQP 프로토콜의 디바이스-클라우드 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 디바이스-클라우드 메시지 수(AMQP 프로토콜)가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_AmqpD2CMessagesNotInAllowedRange |
| 사용자 지정 경고 - 직접 메서드 호출 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 직접 메서드 호출 수가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_DirectMethodInvokesNotInAllowedRange |
| 사용자 지정 경고 - 파일 업로드 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 파일 업로드 수가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_FileUploadsNotInAllowedRange |
| 사용자 지정 경고 - HTTP 프로토콜의 클라우드-디바이스 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 시간 범위에 속한 클라우드-디바이스 메시지(HTTP 프로토콜)의 양이 구성된 허용 범위를 벗어났습니다. | IoT_CA_HttpC2DMessagesNotInAllowedRange |
| 사용자 지정 경고 - HTTP 프로토콜에서 거부된 클라우드-디바이스 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 클라우드-디바이스 메시지 수(HTTP 프로토콜)가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_HttpC2DRejectedMessagesNotInAllowedRange |
| 사용자 지정 경고 - HTTP 프로토콜의 디바이스-클라우드 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 디바이스-클라우드 메시지 수(HTTP 프로토콜)가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_HttpD2CMessagesNotInAllowedRange |
| 사용자 지정 경고 - MQTT 프로토콜의 클라우드-디바이스 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 클라우드-디바이스 메시지 수(MQTT 프로토콜)가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_MqttC2DMessagesNotInAllowedRange |
| 사용자 지정 경고 - MQTT 프로토콜에서 거부된 클라우드-디바이스 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 디바이스에서 거부한 특정 시간 범위 내의 클라우드-디바이스 메시지 수(MQTT 프로토콜)가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_MqttC2DRejectedMessagesNotInAllowedRange |
| 사용자 지정 경고 - MQTT 프로토콜의 디바이스-클라우드 메시지 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 디바이스-클라우드 메시지 수(MQTT 프로토콜)가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_MqttD2CMessagesNotInAllowedRange |
| 사용자 지정 경고 - 명령 큐 제거 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 명령 큐 제거 수가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_QueuePurgesNotInAllowedRange |
| 사용자 지정 경고 - 모듈 쌍 업데이트 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 모듈 쌍 업데이트 수가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_TwinUpdatesNotInAllowedRange |
| 사용자 지정 경고 - 권한 없는 작업 수가 허용 범위를 벗어납니다. | 낮음 | IoT Hub | 특정 시간 범위 내의 권한 없는 작업 수가 현재 구성된 허용 범위를 벗어납니다. | IoT_CA_UnauthorizedOperationsNotInAllowedRange |

## <a name="next-steps"></a>다음 단계

- [경고를 사용자 지정](quickstart-create-custom-alerts.md)하는 방법 알아보기
- Defender for IoT 서비스 [개요](overview.md)
