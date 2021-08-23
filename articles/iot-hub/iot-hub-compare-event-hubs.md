---
title: Azure IoT Hub와 Azure Event Hubs 비교 | Microsoft 문서
description: IoT Hub 및 Event Hubs Azure의 비교는 강조 표시된 기능 차이 및 사용 사례를 제공합니다. 비교는 지원되는 프로토콜, 디바이스 관리, 모니터링 및 파일 업로드를 포함합니다.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 8cd46416690ba5482686f6f4b9caa1f4424d3c20
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566309"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT 디바이스를 Azure에 연결: IoT Hub 및 Event Hubs

Azure는 데이터를 강력한 클라우드에 연결할 수 있도록 다양한 유형의 연결 및 통신을 위해 특별히 개발된 서비스를 제공합니다. Azure IoT Hub와 Azure Event Hub는 모두 대량의 데이터를 수집하고 비즈니스 인사이트를 위해 이 데이터를 처리하거나 저장할 수 있는 클라우드 서비스입니다. 두 서비스는 짧은 대기 시간과 높은 안정성으로 데이터 수집을 지원한다는 점은 비슷하지만 다른 용도로 디자인된 것입니다. IoT Hub는 Azure 클라우드에 IoT 디바이스를 연결하는 고유한 요구 사항을 해결하기 위해 개발되었으며 Event Hubs는 빅 데이터 스트리밍을 위해 디자인되었습니다. IoT 디바이스를 Azure에 연결하려면 Azure IoT Hub를 사용하는 것이 좋습니다.

Azure IoT Hub는 IoT 디바이스를 연결하여 비즈니스 인사이트 및 자동화에 대한 데이터를 수집하는 클라우드 게이트웨이입니다. 또한 IoT Hub에는 디바이스와 백 엔드 시스템 간의 관계를 보강하는 기능이 있습니다. 양방향 통신 기능을 제공하므로 디바이스에서 데이터를 수신하는 동안 디바이스로 다시 명령 및 정책을 전송할 수도 있습니다. 예를 들어 클라우드-디바이스 메시징을 사용하여 속성을 업데이트하거나 디바이스 관리 작업을 호출할 수 있습니다. 클라우드-디바이스 통신을 사용하면 Azure IoT Edge를 사용하여 에지 디바이스에 클라우드 인텔리전스를 보낼 수도 있습니다. IoT Hub에서 제공하는 고유의 디바이스 수준 ID는 잠재적인 공격으로부터 IoT 솔루션을 보다 안전하게 보호합니다. 

[Azure Event Hubs](../event-hubs/event-hubs-about.md)는 Azure의 빅 데이터 스트리밍 서비스입니다. 고객이 하루에 수십 억 건의 요청을 보내는 고 처리량 데이터 스트리밍 시나리오를 위해 설계되었습니다. Event Hubs는 분할된 소비자 모델을 사용하여 스트림을 규모 확장하며 Databricks, Stream Analytics, ADLS, HDInsight를 포함한 Azure의 빅 데이터 및 분석 서비스에 통합됩니다. Event Hubs 캡처 및 자동 확장 같은 기능을 제공하는 이 서비스는 빅 데이터 앱 및 솔루션을 지원하기 위해 설계되었습니다. 또한 IoT Hub는 원격 분석 흐름 경로에 Event Hubs를 사용하므로 IoT 솔루션에서 Event Hubs의 강력한 성능을 사용할 수 있습니다.

요약하자면, 두 솔루션은 대규모 스케일의 데이터 수집을 위해 설계되었습니다. IoT 디바이스를 Azure 클라우드에 연결하여 비즈니스 가치를 최대화하도록 설계된 풍부한 IoT 관련 기능은 IoT Hub에만 있습니다.  IoT를 이제 막 시작한 경우 우선 IoT Hub로 데이터 수집 시나리오를 지원하면 기업 및 기술자들이 기능을 요구할 때 모든 기능을 갖춘 IoT 기능에 즉시 액세스할 수 있습니다.

다음 표는 IoT 기능을 평가할 때 IoT Hub의 두 계층을 Event Hubs와 비교한 상세 정보를 제공합니다. IoT Hub 기본 및 표준 계층에 대한 자세한 내용은 [적합한 IoT Hub 계층 선택 방법](iot-hub-scaling.md)을 참조하세요.

| IoT 기능 | IoT Hub 표준 계층 | IoT Hub 기본 계층 | Event Hubs |
| --- | --- | --- | --- |
| 디바이스-클라우드 메시징 | ![확인][checkmark] | ![확인][checkmark] | ![확인][checkmark] |
| 프로토콜: webSocket을 통한 HTTPS, AMQP, AMQP | ![확인][checkmark] | ![확인][checkmark] | ![확인][checkmark] |
| 프로토콜: webSocket을 통한 MQTT, MQTT | ![확인][checkmark] | ![확인][checkmark] |  |
| 디바이스 단위 ID | ![확인][checkmark] | ![확인][checkmark] |  |
| 디바이스에서 파일 업로드 | ![확인][checkmark] | ![확인][checkmark] |  |
| Device Provisioning Service | ![확인][checkmark] | ![확인][checkmark] |  |
| 클라우드-디바이스 메시징 | ![확인][checkmark] |  |  |
| 디바이스 쌍 및 디바이스 관리 | ![확인][checkmark] |  |  |
| 디바이스 스트림(미리 보기) | ![확인][checkmark] |  |  |
| IoT Edge | ![확인][checkmark] |  |  |

유일한 사용 사례가 디바이스-클라우드 데이터 수집인 경우에도 IoT 디바이스 연결을 위해 설계된 서비스를 제공하는 IoT Hub를 사용하실 것을 강력하게 권장합니다. 

### <a name="next-steps"></a>다음 단계

IoT Hub의 기능에 대해 더 알아보려면 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png