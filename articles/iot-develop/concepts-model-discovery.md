---
title: 솔루션에서 IoT 플러그 앤 플레이 모델 사용 | Microsoft Docs
description: 솔루션 빌더로서 IoT 솔루션에서 IoT 플러그 앤 플레이 모델을 사용하는 방법에 대해 알아봅니다.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 82f845fb87b5b71556dd1638c6694c62655c95a8
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406552"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>IoT 솔루션에서 IoT 플러그 앤 플레이 모델 사용

이 문서에서는 IoT 솔루션에서 IoT 플러그 앤 플레이 디바이스의 모델 ID를 식별한 다음 해당 모델 정의를 검색하는 방법을 설명합니다.

IoT 솔루션에는 두 가지 광범위한 범주가 있습니다.

- *특별 제작된 솔루션* 은 솔루션에 연결할 IoT 플러그 앤 플레이 디바이스용으로 알려진 모델 세트와 함께 작동합니다. 솔루션을 개발할 때 이러한 모델을 사용합니다.

- *모델 기반* 솔루션은 모든 IoT 플러그 앤 플레이 디바이스의 모델과 함께 작동할 수 있습니다. 모델 기반 솔루션을 빌드하는 것은 더 복잡하지만 향후 추가될 수 있는 모든 디바이스에서 솔루션이 작동한다는 이점이 있습니다. 모델 기반 IoT 솔루션은 모델을 검색하고 이를 사용하여 디바이스가 구현하는 원격 분석, 속성 및 명령을 결정합니다.

IoT 플러그 앤 플레이 모델을 사용하는 IoT 솔루션:

1. 솔루션에 연결된 IoT 플러그 앤 플레이 디바이스, 모듈 또는 IoT Edge 모듈에 의해 구현된 모델의 모델 ID를 식별합니다.

1. 모델 ID를 사용하여 모델 리포지토리 또는 사용자 지정 저장소에서 연결된 디바이스의 모델 정의를 검색합니다.

## <a name="identify-model-id"></a>모델 ID 식별

IoT 플러그 앤 플레이 디바이스가 IoT Hub에 연결되면 IoT Hub에 구현하는 모델의 모델 ID를 등록합니다.

IoT Hub는 디바이스 연결 흐름의 일부로 디바이스 모델 ID를 사용하여 솔루션에 알립니다.

솔루션은 다음 세 가지 방법 중 하나를 사용하여 IoT 플러그 앤 플레이 디바이스의 모델 ID를 가져올 수 있습니다.

### <a name="get-device-twin-api"></a>Get Device Twin API

솔루션은 [Get Device Twin API](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) API를 사용하여 IoT 플러그 앤 플레이 디바이스의 모델 ID를 검색할 수 있습니다.

> [!TIP]
> 모듈 및 IoT Edge 모듈의 경우 [ModuleClient.getTwin](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin)을 사용합니다.

다음 디바이스 트윈 응답 조각에서 `modelId`는 IoT 플러그 앤 플레이 디바이스의 모델 ID를 포함합니다.

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>Get Digital Twin API

솔루션은 [Get Digital Twin](/rest/api/iothub/service/digitaltwin/getdigitaltwin) API를 사용하여 IoT 플러그 앤 플레이 디바이스에서 구현된 모델의 모델 ID를 검색할 수 있습니다.

다음 디지털 트윈 응답 조각에서 `$metadata.$model`은 IoT 플러그 앤 플레이 디바이스의 모델 ID를 포함합니다.

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>디지털 트윈 변경 이벤트 알림

디바이스가 연결되면 [디지털 트윈 변경 이벤트](concepts-digital-twin.md#digital-twin-change-events) 알림이 표시됩니다. 솔루션은 이 이벤트 알림을 구독해야 합니다. 디지털 트윈 이벤트에 대한 라우팅을 사용하도록 설정하는 방법을 알아보려면 [IoT Hub 메시지 라우팅을 사용하여 디바이스 간 메시지를 다른 엔드포인트로 보내기](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조하세요.

솔루션은 다음 코드 조각에 표시된 이벤트를 사용하여 연결하고 해당 모델 ID를 가져오는 IoT 플러그 앤 플레이 디바이스에 대해 알아볼 수 있습니다.

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>모델 정의 검색

솔루션은 위에서 식별된 모델 ID를 사용하여 해당 모델 정의를 검색합니다.

솔루션은 다음 옵션 중 하나를 사용하여 모델 정의를 가져올 수 있습니다.

### <a name="model-repository"></a>모델 리포지토리

솔루션은 [모델 리포지토리](concepts-model-repository.md)를 사용하여 모델을 검색할 수 있습니다. 디바이스 빌더 또는 솔루션 빌더는 솔루션이 모델을 검색할 수 있도록 미리 리포지토리에 모델을 업로드해야 합니다.

새 디바이스 연결의 모델 ID를 식별한 후 다음 단계를 따르세요.

1. 모델 리포지토리에서 모델 ID를 사용하여 모델 정의를 검색합니다. 자세한 내용은 [디바이스 모델 리포지토리](concepts-model-repository.md)를 참조하세요.

1. 연결된 디바이스의 모델 정의를 사용하여 디바이스의 기능을 열거할 수 있습니다.

1. 디바이스의 열거된 기능을 사용하여 사용자가 [디바이스와 상호 작용](tutorial-service.md)하도록 허용할 수 있습니다.

### <a name="custom-store"></a>사용자 지정 저장소

솔루션은 이러한 모델 정의를 로컬 파일 시스템, 공용 파일 저장소에 저장하거나 사용자 지정 구현을 사용할 수 있습니다.

새 디바이스 연결의 모델 ID를 식별한 후 다음 단계를 따르세요.

1. 사용자 지정 저장소에서 모델 ID를 사용하여 모델 정의를 검색합니다.

1. 연결된 디바이스의 모델 정의를 사용하여 디바이스의 기능을 열거할 수 있습니다. 

1. 디바이스의 열거된 기능을 사용하여 사용자가 [디바이스와 상호 작용](tutorial-service.md)하도록 허용할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

이제 IoT 솔루션에서 IoT 플러그 앤 플레이 모델을 통합하는 방법을 배웠으므로 몇 가지 제안된 다음 단계는 다음과 같습니다.

- [솔루션에서 디바이스와 상호 작용](tutorial-service.md)
- [IoT 디지털 트윈 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 탐색기](../iot-fundamentals/howto-use-iot-explorer.md)
