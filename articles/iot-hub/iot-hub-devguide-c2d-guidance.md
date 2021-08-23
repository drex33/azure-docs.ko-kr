---
title: Azure IoT Hub 클라우드-디바이스 옵션 | Microsoft Docs
description: 개발자 가이드 - 직접 메서드, 디바이스 쌍의 desired 속성 또는 클라우드-디바이스 통신을 위한 클라우드-디바이스 메시지를 사용하는 경우에 대한 지침입니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 105374dc214ad77978973a5e7288ac1dd487941b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535745"
---
# <a name="cloud-to-device-communications-guidance"></a>클라우드-디바이스 통신 지침

IoT Hub는 백 엔드 앱에 기능을 공개하는 세 가지 옵션을 디바이스 앱에 제공합니다.

* [직접 메서드](iot-hub-devguide-direct-methods.md)는 결과를 즉시 확인해야 하는 통신에 사용됩니다. 대화형 디바이스 제어(예: 선풍기 켜기)에 대해 직접 메서드를 자주 사용합니다.

* [쌍의 원하는 속성](iot-hub-devguide-device-twins.md) 디바이스를 원하는 특정 상태로 만들려는 장기 실행 명령의 경우 예를 들어 원격 분석 송신 간격을 30분으로 설정합니다.

* [클라우드-디바이스 메시지](iot-hub-devguide-messages-c2d.md) 디바이스 앱에 대한 단방향 알림의 경우

[Azure IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md)가 이러한 옵션을 사용하여 IoT 플러그 앤 플레이 디바이스를 제어하는 방법을 알아보려면 [IoT 플러그 앤 플레이 서비스 개발자 가이드](../iot-develop/concepts-developer-guide-service.md)를 참조하세요.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

다음은 다양한 클라우드-디바이스 통신 옵션에 대해 자세히 비교하고 있습니다.

| 범주 | 직접 메서드 | 쌍의 desired 속성 | 클라우드-디바이스 메시지 |
| ---------- | -------------- | ------------------------- | ------------------------ |
| 시나리오 | 즉각적인 확인이 필요한 명령(예: 팬 작동) | 디바이스를 원하는 상태로 만들려는 장기 실행 명령. 예를 들어 원격 분석 송신 간격을 30분으로 설정합니다. | 디바이스 앱에 대한 단방향 알림 |
| 데이터 흐름 | 양방향. 디바이스 앱에서 메서드에 즉시 응답할 수 있습니다. 솔루션 백 엔드에서 컨텍스트에 따라 요청에 대한 결과를 수신합니다. | 단방향. 디바이스 앱에서 속성 변경 알림을 수신합니다. | 단방향. 디바이스 앱에서 메시지를 수신합니다.
| 내구성 | 연결이 끊긴 디바이스는 연결되지 않습니다. 디바이스가 연결되어 있지 않다고 솔루션 백 엔드에 알립니다. | 속성 값은 디바이스 쌍에 유지됩니다. 다음에 다시 연결할 때 디바이스에서 이 알림을 읽습니다. 속성 값은 [IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)로 검색할 수 있습니다. | 메시지는 최대 48시간 동안 IoT Hub에 보관될 수 있습니다. |
| 대상 | **deviceId** 를 사용하는 단일 디바이스 또는 [jobs](iot-hub-devguide-jobs.md)를 사용하는 여러 디바이스 | **deviceId** 를 사용하는 단일 디바이스 또는 [jobs](iot-hub-devguide-jobs.md)를 사용하는 여러 디바이스 | **deviceId** 를 사용하는 단일 디바이스 |
| 크기 | 최대 직접 메서드 페이로드 크기는 128KB입니다. | 최대 희망 속성 크기는 32KB입니다. | 최대 64KB 메시지 |
| 빈도 | 높음. 자세한 내용은 [IoT Hub 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요. | 중간. 자세한 내용은 [IoT Hub 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요. | 낮음. 자세한 내용은 [IoT Hub 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요. |
| 프로토콜 | MQTT 또는 AMQP를 통해 사용 가능합니다. | MQTT 또는 AMQP를 통해 사용 가능합니다. | 모든 프로토콜에서 사용할 수 있습니다. HTTPS를 사용할 경우 디바이스에서 폴링해야 합니다. |

다음 자습서에서 직접 메서드, desired 속성 및 클라우드-디바이스 메시지를 사용하는 방법에 대해 알아보세요.

* [직접 메서드 사용](quickstart-control-device.md)
* [desired 속성을 사용하여 디바이스 구성](tutorial-device-twins.md) 
* [클라우드-디바이스 메시지 보내기](iot-hub-node-node-c2d.md)
