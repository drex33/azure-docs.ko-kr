---
title: Azure IoT SDK 없이 개발 | Microsoft Docs
description: 개발자 가이드 - Azure IoT SDK를 사용하지 않고 디바이스 앱 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 항목에 대한 정보 및 링크입니다.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 7da7d19debdf8068004473316985370900f0c7b2
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555095"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Azure IoT Hub SDK를 사용하지 않고 개발

이 항목에서는 Azure IoT SDK를 사용하지 않고 디바이스 또는 백 엔드 앱을 개발하려는 개발자에게 유용한 정보와 링크를 제공합니다.

Microsoft는 Azure IoT SDK 사용을 적극 권합니다. Azure IoT 디바이스 및 서비스 SDK는 여러 유명 플랫폼에 게시됩니다. SDK는 디바이스 연결 및 다시 연결, 재시도 정책을 포함하여 기본 통신 프로토콜의 복잡성을 대부분 처리하는 편리한 계층을 제공합니다. SDK는 정기적으로 업데이트되어 IoT Hub에 최신 기능과 보안 업데이트를 제공합니다. SDK를 사용하면 개발 시간과 코드 유지 관리로 인한 시간을 축소할 수 있습니다. Azure IoT SDK에 대한 자세한 내용은 [Azure IoT 디바이스 및 서비스 SDK](iot-hub-devguide-sdks.md)를 참조하세요. Azure IoT SDK를 사용하는 이점에 대한 세부 정보는 블로그 게시물 [Azure IoT SDK 사용으로 인한 이점과 사용하지 않을 때 피해야 할 위험 요소](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)를 참조하세요.

IoT Hub는 디바이스와의 통신을 위해 AMQP, WebSockets를 통한 AMQP, HTTPS, MQTT, WebSockets를 통한 MQTT를 지원하지만 디바이스에서 지원하는 경우 MQTT를 사용하는 것이 좋습니다.

## <a name="development-prerequisites"></a>개발 필수 조건

개발을 시작하기 전에 디바이스 또는 백엔드 앱에서 구현하려는 기능 및 IoT Hub에 대한 철저한 지식이 있어야 합니다. 다음은 잘 알고 있어야 하는 매우 축약된 토픽 목록입니다.

* IoT Hub에 의해 공개되는 엔드포인트와 각 엔드포인트에서 지원되는 프로토콜을 이해해야 합니다. 자세한 내용은 [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)를 참조하세요.

* 선택한 프로토콜이 디바이스 앱과 관련된 경우 MQTT를 사용하는 것이 좋습니다. 그러나 프로토콜을 선택하기 전에 각각에 의해 부과되는 제한 사항을 이해해야 합니다. 자세한 내용은 [통신 프로토콜 선택](iot-hub-devguide-protocols.md)을 참조하세요.

* IoT Hub 인증을 이해하려면 [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)를 참조하세요.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>다른 프로토콜에 대한 도움말

Azure IoT SDK 없이 다음 프로토콜을 사용하는 데 도움이 되는 경우는 다음과 같습니다.

* **AMQP** 의 디바이스 또는 백엔드 앱은 [AMQP 지원](iot-hub-amqp-support.md)을 참조하세요.

* **MQTT** 의 디바이스 앱에 대해서는 [MQTT 지원](iot-hub-mqtt-support.md)을 참조하세요. 이 토픽의 대부분은 MQTT 프로토콜 직접 사용을 처리합니다. [IoT MQTT 샘플 리포지토리](https://github.com/Azure-Samples/IoTMQTTSample)를 사용하는 방법에 대한 정보도 포함됩니다. 이 리포지토리에는 Eclipse Mosquitto 라이브러리를 사용하여 IoT Hub로 메시지를 보내는 C 샘플이 포함되어 있습니다.

* **HTTPS** 의 디바이스 또는 백 엔드 앱은 [Azure IoT Hub REST API](/rest/api/iothub/)를 참조하세요. [개발 필수 조건](#development-prerequisites)에서 설명한 대로 HTTPS로 x.509 CA(인증 기관) 인증서를 사용할 수 없습니다.

디바이스의 경우 디바이스에서 지원한다면 MQTT를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [MQTT 지원](iot-hub-mqtt-support.md)