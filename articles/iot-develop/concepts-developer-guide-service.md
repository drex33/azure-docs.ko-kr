---
title: 서비스 개발자 가이드 - IoT 플러그 앤 플레이 | Microsoft Docs
description: 서비스 개발자를 위한 IoT 플러그 앤 플레이 설명
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 15a381f847082e0853db307cc60893eaeeb43e96
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122604162"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT 플러그 앤 플레이 서비스 개발자 가이드

IoT 플러그 앤 플레이를 통해 Azure IoT 어플리케이션에 기능을 알리는 IoT 디바이스를 구축할 수 있습니다. IoT 플러그 앤 플레이 디바이스는 고객이 IoT 플러그 앤 플레이 지원 애플리케이션에 연결할 때 수동 구성이 필요하지 않습니다.

IoT 플러그 앤 플레이를 사용하면 IoT 허브로 모델 ID를 발표한 디바이스를 사용할 수 있습니다. 예를 들어 디바이스의 속성 및 명령에 직접 액세스할 수 있습니다.

IoT 허브에 연결된 IoT 플러그 앤 플레이 디바이스를 사용하려면 IoT 서비스 SDK 중 하나를 사용합니다.

## <a name="service-sdks"></a>서비스 SDK

솔루션에서 Azure IoT 서비스 SDK를 사용하여 디바이스 및 모듈과 상호 작용합니다. 예를 들어, 서비스 SDK를 사용하여 쌍 속성을 읽고, 업데이트하고, 명령을 호출할 수 있습니다. 지원되는 언어에는 C#, Java, Node.js, Python이 포함됩니다.

서비스 SDK를 사용하면 데스크톱 또는 웹 애플리케이션과 같은 솔루션에서 디바이스 정보에 액세스할 수 있습니다. 서비스 SDK에는 모델 ID를 검색하는 데 사용할 수 있는 두 개의 네임스페이스와 개체 모델이 포함되어 있습니다.

- IoT Hub 서비스 클라이언트. 이 서비스는 모델 ID를 디바이스 쌍 속성으로 노출합니다.

- Digital Twins 클라이언트. 새 Digital Twins API는 구성 요소, 속성, 명령과 같은 [DTDL(디지털 쌍 정의 언어)](concepts-digital-twin.md) 모델 구조에서 작동합니다. 디지털 쌍 API를 사용하면 솔루션 빌더에서 IoT 플러그 앤 플레이 솔루션을 쉽게 만들 수 있습니다.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>다음 단계

이제 디바이스 모델링에 대해 알아보았으므로 다음과 같은 추가 리소스가 있습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT 플러그 앤 플레이 모델링 가이드](concepts-modeling-guide.md)