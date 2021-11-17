---
title: Azure IoT Hub 엔드포인트 이해 | Microsoft Docs
description: 개발자 가이드 - IoT Hub 디바이스 지향 및 서비스 지향 엔드포인트에 대한 참조 정보
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: e320fd286d9bde8cbc02a45815c662f6a2fd26fe
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548945"
---
# <a name="reference---iot-hub-endpoints"></a>참조 - IoT Hub 엔드포인트

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub 이름

포털의 허브 **개요** 페이지에서 엔드포인트를 호스트하는 IoT 허브의 호스트 이름을 찾을 수 있습니다. 기본적으로 IoT Hub의 DNS 이름은 다음과 같은 형태입니다. `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>기본 제공 IoT Hub 엔드포인트 목록

Azure IoT Hub는 다중 테넌트 서비스이며 다양한 행위자에게 기능을 노출합니다. 아래 다이어그램에서는 IoT Hub가 노출하는 다양한 엔드포인트를 보여줍니다.

![IoT Hub 엔드포인트](./media/iot-hub-devguide-endpoints/endpoints.png)

다음 목록에서는 엔드포인트를 설명합니다.

* **리소스 공급자**. IoT Hub 리소스 공급자는 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 인터페이스를 노출합니다. 이 인터페이스를 통해 Azure 구독 소유자는 IoT Hub를 생성 및 삭제하고 IoT Hub 속성을 업데이트할 수 있습니다. IoT Hub 속성은 디바이스 수준 액세스 제어와는 달리 [허브 수준 보안 정책](iot-hub-dev-guide-sas.md#access-control-and-permissions)과 클라우드-디바이스 및 디바이스-클라우드 메시징을 위한 기능 옵션을 관리합니다. 또한 IoT Hub 리소스 공급자는 사용자가 [디바이스 ID를 내보낼](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) 수 있도록 합니다.

* **디바이스 ID 관리**. 각 IoT Hub는 HTTPS REST 엔드포인트 집합을 노출하여 디바이스 ID를 관리합니다(만들기, 검색, 업데이트 및 삭제). [디바이스 ID](iot-hub-devguide-identity-registry.md)는 디바이스 인증 및 액세스 제어에 사용됩니다.

* **디바이스 쌍 관리**. 각 IoT Hub에서 [디바이스 쌍](iot-hub-devguide-device-twins.md)(태그 및 속성 업데이트)을 쿼리하고 업데이트하기 위해 서비스 지향 HTTPS REST 엔드포인트 집합을 공개합니다. 

* **작업 관리**. 각 IoT Hub에서 [작업](iot-hub-devguide-jobs.md)을 쿼리하고 업데이트하기 위해 서비스 지향 HTTPS REST 엔드포인트 집합을 공개합니다.

* **디바이스 엔드포인트**. IoT Hub는 ID 레지스트리의 각 디바이스에 대해 엔드포인트 집합을 노출합니다. 달리 명시된 경우를 제외하고, 엔드포인트는 [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1, [AMQP 1.0](https://www.amqp.org/) 프로토콜을 사용하여 노출됩니다. AMQP 및 MQTT는 포트 443에서 [WebSockets](https://tools.ietf.org/html/rfc6455)을 통해 사용할 수도 있습니다.

  * *디바이스-클라우드 메시지 보내기*. 디바이스는 이 엔드포인트를 사용하여 [디바이스-클라우드 메시지를 보냅니다](iot-hub-devguide-messages-d2c.md).

  * *클라우드-디바이스 메시지 받기*. 디바이스는 이 엔드포인트를 사용하여 대상인 [클라우드-디바이스 메시지](iot-hub-devguide-messages-c2d.md)를 받습니다.

  * *파일 업로드를 시작합니다*. 디바이스는 이 엔드포인트를 사용하여 [파일을 업로드](iot-hub-devguide-file-upload.md)하기 위해 IoT Hub에서 Azure Storage SAS URI를 받습니다.

  * *디바이스 쌍 속성 검색 및 업데이트*. 디바이스에서 이 엔드포인트를 사용하여 [디바이스 쌍](iot-hub-devguide-device-twins.md) 속성에 액세스합니다. HTTPS는 지원되지 않습니다.

  * *직접 메서드 요청 수신*. 디바이스에서 이 엔드포인트를 사용하여 [직접 메서드](iot-hub-devguide-direct-methods.md) 요청을 수신합니다. HTTPS는 지원되지 않습니다.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **서비스 엔드포인트**. 각 IoT Hub는 솔루션 백 엔드에서 디바이스와 통신하기 위한 엔드포인트 집합을 노출합니다. 한 가지 예외를 제외하고 엔드포인트는 [AMQP](https://www.amqp.org/) 및 WebSocket을 통한 AMQP 프로토콜을 사용하여 노출됩니다. 직접 메서드 호출 엔드포인트는 HTTPS 프로토콜을 통해 노출됩니다.
  
  * *디바이스-클라우드 메시지 받기*. 이 엔드포인트는 [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)와 호환됩니다. 백 엔드 서비스는 이 엔드포인트를 사용하여 디바이스에서 보낸 모든 [디바이스-클라우드 메시지](iot-hub-devguide-messages-d2c.md)를 읽을 수 있습니다. 이 기본 제공 엔드포인트 외에도 IoT Hub에 사용자 지정 엔드포인트를 만들 수 있습니다.
  
  * *클라우드-디바이스 메시지를 보내고 배달 승인 받기*. 이러한 엔드포인트를 사용하면 솔루션 백 엔드에서 신뢰할 수 있는 [클라우드-디바이스 메시지](iot-hub-devguide-messages-c2d.md)를 전송하고 해당 전달 또는 만료 승인을 수신할 수 있습니다.
  
  * *파일 알림을 받습니다*. 이 메시징 엔드포인트를 사용하면 디바이스가 성공적으로 파일을 업로드하는 경우 알림을 받을 수 있습니다. 
  
  * *직접 메서드 호출*. 이 엔드포인트를 사용하면 백 엔드 서비스가 디바이스에서 [직접 메서드](iot-hub-devguide-direct-methods.md)를 호출할 수 있습니다.
  
  * *작업 모니터링 이벤트를 수신합니다*. 이 엔드포인트를 통해 IoT Hub가 작업 모니터링 이벤트를 내보내도록 구성된 경우 작업 모니터링 이벤트를 수신할 수 있습니다. 자세한 내용은 [IoT Hub 작업 모니터링](iot-hub-operations-monitoring.md)을 참조하세요.

[Azure IoT SDK](iot-hub-devguide-sdks.md) 문서에서는 이러한 엔드포인트에 액세스할 수 있는 다양한 방법에 대해 설명합니다.

모든 IoT Hub 엔드포인트는 [TLS](https://tools.ietf.org/html/rfc5246) 프로토콜을 사용하고 어떤 엔드포인트도 암호화되지/보안되지 않은 채널에 공개되지 않습니다.

## <a name="custom-endpoints"></a>사용자 지정 엔드포인트

Azure 구독의 기존 Azure 서비스를 IoT 허브에 연결하여 메시지 라우팅을 위한 엔드포인트 역할을 할 수 있습니다. 이러한 엔드포인트는 서비스 엔드포인트 역할을 하며 메시지 경로에 대한 싱크로 사용됩니다. 디바이스는 추가 엔드포인트에 직접 쓸 수 없습니다. [메시지 라우팅](../iot-hub/iot-hub-devguide-messages-d2c.md)에 대해 알아봅니다.

IoT Hub는 현재 추가 엔드포인트로 다음과 같은 Azure 서비스를 지원합니다.

* Azure Storage 컨테이너
* Event Hubs
* Service Bus 큐
* Service Bus 토픽

추가할 수 있는 엔드포인트의 수에 대한 제한은 [할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요.

## <a name="endpoint-health"></a>엔드포인트 상태

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>현장 게이트웨이

IoT 솔루션에서 *필드 게이트웨이* 는 디바이스와 IoT Hub 엔드포인트 사이에 위치하며 일반적으로 디바이스 가까이에 위치합니다. 디바이스는 해당 디바이스에서 지원되는 프로토콜을 사용하여 필드 게이트웨이와 직접 통신합니다. 필드 게이트웨이는 IoT Hub에서 지원하는 프로토콜을 사용하여 IoT Hub 엔드포인트에 연결됩니다. 필드 게이트웨이는 전용 하드웨어 디바이스이거나 사용자 지정 게이트웨이 소프트웨어를 실행하는 저전력 컴퓨터일 수 있습니다.

[Azure IoT Edge](../iot-edge/index.yml)를 사용하여 필드 게이트웨이를 구현할 수 있습니다. IoT Edge는 여러 디바이스에서 동일한 IoT Hub 연결로의 통신 다중 송신과 같은 기능을 제공합니다.

## <a name="next-steps"></a>다음 단계

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [디바이스 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)
* [할당량 및 제한](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT 지원](iot-hub-mqtt-support.md)
* [IoT 허브 IP 주소 이해](iot-hub-understand-ip-address.md)
