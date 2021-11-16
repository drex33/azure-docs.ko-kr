---
title: Azure IoT 프로토콜 게이트웨이 | Microsoft Docs
description: Azure IoT 프로토콜 게이트웨이를 사용하여 IoT Hub 기능과 프로토콜 지원을 확장하여 IoT Hub에서 기본적으로 지원되지 않는 프로토콜을 사용하여 사용자 허브에 디바이스를 연결할 수 있게 하는 방법입니다.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/19/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6874c4d55395dea7e58d27d0cd7ba1d159e21dee
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552511"
---
# <a name="support-additional-protocols-for-iot-hub"></a>IoT Hub에 대한 추가 프로토콜 지원

Azure IoT Hub는 MQTT, AMQP, HTTPS 프로토콜을 통한 통신을 기본적으로 지원합니다. 일부 경우 디바이스 또는 필드 게이트웨이에서 이러한 표준 프로토콜 중 하나를 사용하지 못할 수 있으므로 프로토콜 적응이 필요합니다. 이러한 경우 사용자 지정 게이트웨이를 사용할 수 있습니다. 사용자 지정 게이트웨이에서 IoT Hub로(부터)의 트래픽을 브리징하여 IoT Hub 엔드포인트에 대한 프로토콜 적응을 사용할 수 있습니다. [Azure IoT 프로토콜 게이트웨이](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) 를 사용자 지정 게이트웨이로 사용하여 IoT Hub에 대한 프로토콜 적응을 사용할 수 있습니다.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT 프로토콜 게이트웨이

Azure IoT 프로토콜 게이트웨이는 프로토콜 적응을 위한 프레임워크로 IoT Hub와 확장성이 뛰어난 양방향 디바이스 통신을 위해 디자인되었습니다. 프로토콜 게이트웨이는 특정 프로토콜을 통한 디바이스 연결을 수락하는 통과 구성 요소입니다. AMQP 1.0을 통해 IoT Hub에 트래픽을 브리징합니다.

Azure Service Fabric, Azure Cloud Services 작업자 역할 또는 Windows Virtual Machines를 사용하여 확장성이 뛰어난 방식으로 Azure에서 프로토콜 게이트웨이를 배포할 수 있습니다. 또한 프로토콜 게이트웨이는 필드 게이트웨이와 같은 온-프레미스 환경에서 배포될 수 있습니다.

Azure IoT 프로토콜 게이트웨이는 필요한 경우 MQTT 프로토콜 동작을 사용자 지정할 수 있는 MQTT 프로토콜 어댑터를 포함합니다. IoT Hub는 MQTT v3.1.1 프로토콜을 기본적으로 지원하기 때문에, 프로토콜 사용자 지정이나 추가 기능을 위한 특정 요구 사항이 있는 경우 MQTT 프로토콜 어댑터 사용을 고려해야 합니다.

또한 MQTT 어댑터는 다른 프로토콜에 대한 프로토콜 어댑터를 빌드하기 위한 프로그래밍 모델을 보여 줍니다. 이외에도 Azure IoT 프로토콜 게이트웨이 프로그래밍 모델을 사용하면 사용자 지정 인증, 메시지 변환, 압축/압축 풀기 또는 디바이스와 IoT Hub 간 트래픽 암호화/암호 해독과 같은 특수한 처리를 위한 사용자 지정 구성 요소에 연결할 수 있습니다.

유연성을 위해 Azure IoT 프로토콜 게이트웨이 및 MQTT 구현은 오픈 소스 소프트웨어 프로젝트에서 제공됩니다. 오픈 소스 프로젝트를 사용하여 다양한 프로토콜 및 프로토콜 버전에 대한 지원을 추가하거나 시나리오에 맞게 구현을 사용자 지정할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Azure IoT 프로토콜 게이트웨이와 이를 IoT 솔루션의 일부로 사용하고 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [GitHub에서 Azure IoT 프로토콜 게이트웨이 리포지토리](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Azure IoT 프로토콜 게이트웨이 개발자 가이드](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

IoT Hub 배포를 계획하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Event Hubs와 비교](iot-hub-compare-event-hubs.md)

* [크기 조정, 고가용성 및 재해 복구](iot-hub-scaling.md)

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)