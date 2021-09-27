---
title: 디바이스 개발자 가이드 (C)-IoT 플러그 앤 플레이 | Microsoft Docs
description: C 디바이스 개발자를 위한 IoT 플러그 앤 플레이 설명
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
zone_pivot_groups: programming-languages-set-twenty-seven
ms.openlocfilehash: 528f1270be0065622ba7c79db33f8e4eed93bd74
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580479"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT 플러그 앤 플레이 디바이스 개발자 가이드

IoT 플러그 앤 플레이를 통해 Azure IoT 어플리케이션에 기능을 알리는 IoT 디바이스를 구축할 수 있습니다. IoT 플러그 앤 플레이 디바이스는 고객이 IoT 플러그 앤 플레이 지원 애플리케이션에 연결할 때 수동 구성이 필요하지 않습니다.

IoT 디바이스는 직접 실행할 수도 있고, [모듈](../iot-hub/iot-hub-devguide-module-twins.md)을 사용하거나, [IoT Edge 모듈](../iot-edge/about-iot-edge.md)을 사용할 수 있습니다.

이 가이드에서는 [IoT 플러그 앤 플레이 방식](../iot-develop/concepts-convention.md)을 따르는 디바이스, 모듈 또는 IoT Edge 모듈을 만드는 데 필요한 기본 단계에 대해 설명합니다.

IoT 플러그 앤 플레이 디바이스, 모듈 또는 IoT Edge 모듈을 구축하려면 다음 단계를 수행합니다:

1. 디바이스가 MQTT 또는 Websocket을 통한 MQTT 프로토콜을 사용하여 Azure IoT Hub에 연결하는지 확인합니다.
1. 디바이스를 설명하는 [Digital Twins 정의어(DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 모델을 만듭니다. 자세한 내용은 [IoT 플러그 앤 플레이 모델의 구성 요소 이해](concepts-modeling-guide.md)를 참조하십시오.
1. 디바이스 연결의 일부를 알리기 위해 디바이스 또는 모듈을 업데이트`model-id`합니다.
1. [IoT 플러그 앤 플레이 방식](concepts-convention.md)을 사용한 원격 분석, 속성 및 명령 구현

디바이스 또는 모듈 구현이 준비되면 [Azure IoT 탐색기](../iot-fundamentals/howto-use-iot-explorer.md)를 사용하여 디바이스가 IoT 플러그 앤 플레이 방식을 따르는지 확인합니다.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-embedded-c"

[!INCLUDE [iot-pnp-device-devguide-embedded-c](../../includes/iot-pnp-device-devguide-embedded-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>다음 단계

이제 IoT 플러그 앤 플레이 디바이스 개발에 대해 살펴보았습니다. 몇 가지 추가 리소스는 다음과 같습니다:

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT 플러그 앤 플레이 모델의 구성 요소 이해](concepts-modeling-guide.md)
- [IoT 플러그 앤 플레이 디바이스 개발자 가이드](concepts-developer-guide-service.md)
