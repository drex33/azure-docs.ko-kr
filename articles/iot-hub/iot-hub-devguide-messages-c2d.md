---
title: Azure IoT Hub 클라우드-디바이스 메시징 이해 | Microsoft Docs
description: 이 개발자 가이드에서는 IoT Hub에서 클라우드-디바이스 메시징을 사용하는 방법을 설명합니다. 메시지 수명 주기 및 구성 옵션에 대한 정보를 포함합니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: f712f3f9928816e237504f504ec8ef28ec45d0e6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263569"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>IoT Hub에서 클라우드-디바이스 메시지 보내기

솔루션 백 엔드에서 디바이스 앱으로 단방향 알림을 보내려면 IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보냅니다. Azure IoT Hub가 지원하는 다른 클라우드-디바이스 옵션에 대한 설명은 [클라우드-디바이스 통신 지침](iot-hub-devguide-c2d-guidance.md)을 참조하세요.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

서비스 지향 엔드포인트 */messages/devicebound* 를 통해 클라우드-디바이스 메시지를 보냅니다. 그런 다음, 디바이스는 디바이스별 엔드포인트 */devices/{deviceId}/messages/devicebound* 를 통해 메시지를 수신합니다.

단일 디바이스에서 각 클라우드-디바이스 메시지를 대상으로 지정하려면 IoT Hub는 **to** 속성을 */devices/{deviceId}/messages/devicebound* 로 설정합니다.

각 디바이스 큐는 클라우드-디바이스 메시지를 최대 50개까지 보유합니다. 동일한 디바이스에 더 많은 메시지를 보내려고 하면 오류가 발생합니다.

## <a name="the-cloud-to-device-message-life-cycle"></a>클라우드-디바이스 메시지 수명 주기

메시지 전달을 한 번 이상 보장하기 위해 IoT Hub는 디바이스 별 큐에 클라우드-디바이스 메시지를 유지합니다. IoT Hub가 큐에서 메시지를 제거하기 위해서는 디바이스가 *완료* 를 명시적으로 인정해야 합니다. 이 방법은 연결 및 디바이스 오류로부터 복원력을 보장합니다.

수명 주기 상태 그래프가 다음 다이어그램에 표시됩니다.

![클라우드-디바이스 메시지 수명 주기](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

IoT Hub 서비스가 디바이스에 메시지를 보내면 서비스는 메시지 상태를 *큐에 넣음* 으로 설정합니다. 디바이스에서 메시지를 *수신* 하려고 하면 IoT Hub는 상태를 숨김으로 설정하여 메시지를 ‘잠급니다’. 이 상태에서는 디바이스의 다른 스레드가 다른 메시지 수신을 시작할 수 있습니다. 디바이스 스레드가 메시지의 처리를 완료하면 IoT Hub에 메시지를 *완료* 했다고 알립니다. 그런 다음, IoT Hub는 상태를 *완료* 로 설정합니다.

또한 디바이스는 다음을 수행할 수 있습니다.

* 메시지 *거부*. 이 경우 IoT Hub는 메시지를 *Dead lettered* 상태로 설정합니다. MQTT(메시지 큐 원격 분석 전송) 프로토콜을 통해 연결하는 디바이스는 클라우드-디바이스 메시지를 거부할 수 없습니다.

* 메시지 *중단*. 이 경우 IoT Hub는 상태를 *큐에 넣음* 으로 설정하여 메시지를 큐에 다시 넣습니다. MQTT 프로토콜을 통해 연결하는 디바이스는 클라우드-디바이스 메시지를 중단할 수 없습니다.

스레드는 IoT Hub에 알리지 않고 메시지를 처리하는 데 실패할 수 있습니다. 이 경우 표시 제한 시간 또는 잠금 제한 시간 후에 메시지는 숨김 상태에서 큐에 넣음 상태로 자동 전환됩니다. 이 제한 시간 값은 1분이며 변경할 수 없습니다.

IoT Hub의 **최대 배달 횟수** 속성은 *큐에 넣음* 및 *숨김* 상태 간에 메세지가 전환할 수 있는 최대 횟수를 결정합니다. 해당 전환 횟수 후에 IoT Hub는 메시지의 상태를 *Dead lettered* 로 설정합니다. 마찬가지로 IoT Hub는 만료 시간 후에 메시지의 상태를 Dead lettered로 설정합니다. 자세한 내용은 TTL([Time to Live](#message-expiration-time-to-live))을 참조하세요.

[IoT Hub를 사용하여 클라우드-디바이스 메시지를 보내는 방법](iot-hub-csharp-csharp-c2d.md) 문서에서는 클라우드에서 클라우드-디바이스 메시지를 보내고 디바이스에서 수신하는 방법을 보여 줍니다.

일반적으로 메시지 손실이 애플리케이션 논리에 영향을 주지 않으면 디바이스가 클라우드-디바이스 메시지를 완료합니다. 디바이스가 메시지 콘텐츠를 로컬에 유지하거나 작업을 성공적으로 실행한 경우를 예로 들 수 있습니다. 또한 메시지가 임시 정보를 전달하고 있으므로 손실되더라도 애플리케이션의 기능에 영향을 주지 않을 수도 있습니다. 경우에 따라 장기 실행 작업에 다음을 수행할 수 있습니다.

* 디바이스가 로컬 스토리지에 작업 설명을 유지한 후 클라우드-디바이스 메시지를 완료합니다.

* 작업이 진행되는 다양한 단계에서 하나 이상의 디바이스-클라우드 메시지를 사용하여 솔루션 백 엔드에 알림을 제공할 수 있습니다.

## <a name="message-expiration-time-to-live"></a>메시지 만료(TTL(Time To Live))

모든 클라우드-디바이스 메시지에는 만료 시간이 있습니다. 이 시간은 다음 중 하나로 설정됩니다.

* 서비스의 **ExpiryTimeUtc** 속성
* IoT Hub: IoT Hub 속성으로 지정된 기본 TTL(*Time to live*) 사용

[클라우드-디바이스 구성 옵션](#cloud-to-device-configuration-options)을 참조하세요.

메시지 만료를 활용하여 연결되지 않은 디바이스에 메시지 보내기를 방지하는 일반적인 방법은 *TTL(Time to Live)* 값을 짧게 설정하는 것입니다. 이 방법은 디바이스 연결 상태를 유지 관리하는 것과 동일한 결과를 내지만 좀 더 효율적입니다. 메시지 승인을 요청하는 경우 IoT Hub는 다음과 같은 디바이스에 대해 알립니다.

* 메시지를 받을 수 없는 경우
* 온라인 상태가 아니거나 실패한 경우

## <a name="message-feedback"></a>메시지 피드백

클라우드-디바이스 메시지를 보낼 때 서비스는 해당 메시지의 최종 상태에 대한 메시지 단위 피드백을 전달하도록 요청할 수 있습니다. 이렇게 하려면 다음 4개 값 중 하나로 전송되는 클라우드-디바이스 메시지에서 **iothub-ack** 애플리케이션 속성을 설정합니다.

| Ack 속성 값 | 동작 |
| ------------ | -------- |
| 없음     | IoT Hub가 피드백 메시지를 생성하지 않습니다(기본 동작). |
| 긍정 | 클라우드-디바이스 메시지가 *완성* 상태인 경우 IoT Hub가 피드백 메시지를 생성합니다. |
| 부정 | 클라우드-디바이스 메시지가 *Dead lettered* 상태인 경우 IoT Hub가 피드백 메시지를 생성합니다. |
| 전체     | IoT Hub는 어떤 경우든 피드백 메시지를 생성합니다. |

**Ack** 값이 *full* 이고 피드백 메시지를 수신하지 못한 경우 피드백 메시지가 만료되었음을 의미합니다. 서비스는 원본 메시지에서 발생한 상황을 알지 못합니다. 실제로 서비스는 만료되기 전에 피드백을 처리할 수 있는지 확인해야 합니다. 오류가 발생한 경우 서비스를 다시 가동하는 시간을 남겨두는 최대 만료 시간이 2일로 지정되어 있습니다.

[엔드포인트](iot-hub-devguide-endpoints.md)에 설명된 대로 IoT Hub는 서비스 지향 엔드포인트(‘messages/servicebound/feedback’)를 통해 피드백을 메시지로 전달합니다. 피드백 수신을 위한 의미 체계는 클라우드-디바이스 메시지의 경우와 같습니다. 가능한 경우 메시지 피드백은 다음 형식으로 단일 메시지에서 일괄 처리됩니다.

| 속성     | Description |
| ------------ | ----------- |
| EnqueuedTime | 허브에서 피드백 메시지를 받은 시기를 나타내는 타임스탬프입니다. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

일괄 처리가 64개 메시지에 도달하거나 마지막으로 전송하고 15초 이내에(두 가지 중 먼저 나오는 경우) 시스템에서 피드백을 보냅니다. 

본문은 각각 다음과 같은 속성이 있는 레코드의 JSON으로 직렬화된 배열입니다.

| 속성           | 설명 |
| ------------------ | ----------- |
| enqueuedTimeUtc    | 메시지의 결과가 발생한 시간을 나타내는 타임스탬프입니다(예를 들어 허브에서 피드백 메시지를 받았거나 원래 메시지가 만료됨). |
| OriginalMessageId  | 이 피드백 정보와 관련된 클라우드-디바이스 메시지의 *MessageId* 입니다. |
| statusCode         | IoT Hub에서 생성된 피드백 메시지에 사용되는 다음과 같은 필수 문자열입니다. <br/> *Success* <br/> *만료됨* <br/> *DeliveryCountExceeded* <br/> *거부됨* <br/> *Purged* |
| description        | *StatusCode* 에 대한 문자열 값입니다. |
| deviceId           | 피드백의 해당 부분과 관련된 클라우드-디바이스 메시지에서 대상 디바이스의 *DeviceId* 입니다. |
| deviceGenerationId | 피드백의 해당 부분과 관련된 클라우드-디바이스 메시지에서 대상 디바이스의 *DeviceGenerationId* 입니다. |

서비스는 원본 메시지와 해당 피드백을 상호 연결하기 위해 클라우드-디바이스 메시지에 대한 *MessageId* 를 지정해야 합니다.

피드백 메시지의 본문은 다음 코드에 나와 있습니다.

```json
[
  {
    "originalMessageId": "0987654321",
    "enqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "statusCode": "Success",
    "description": "Success",
    "deviceId": "123",
    "deviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**삭제된 디바이스에 대한 보류 중인 피드백**

디바이스를 삭제하면 보류 중인 피드백도 모두 삭제됩니다. 디바이스 피드백이 일괄로 전송됩니다. 디바이스가 메시지 수신을 확인하고 다음 피드백 일괄 처리가 준비될 때까지 좁은 기간(종종 1초 미만) 내에 디바이스를 삭제하는 경우 피드백은 발생하지 않습니다.

디바이스를 삭제하기 전에 보류 중인 피드백이 도착할 때까지 대기하여 이 동작을 처리할 수 있습니다. 디바이스가 삭제되면 관련 메시지 피드백이 손실되는 것으로 간주됩니다.

## <a name="cloud-to-device-configuration-options"></a>클라우드-디바이스 구성 옵션

각 IoT Hub는 클라우드-디바이스 메시징에 다음 구성 옵션을 노출합니다.

| 속성                  | Description | 범위 및 기본값 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 클라우드-디바이스 메시지에 대한 기본 TTL | ISO_8601 간격은 최대 2일(최소 1분)입니다. 기본값: 1시간 |
| maxDeliveryCount          | 클라우드-디바이스 디바이스 단위 큐에 대한 최대 전달 수입니다. | 1~100, 기본값: 10 |
| feedback.ttlAsIso8601     | 서비스 바인딩 피드백 메시지에 대한 보존 기간입니다. | ISO_8601 간격은 최대 2일(최소 1분)입니다. 기본값: 1시간 |
| feedback.maxDeliveryCount | 피드백 큐에 대한 최대 전달 수입니다. | 1~100, 기본값: 10 |
| feedback.lockDurationAsIso8601 | 피드백 큐의 잠금 기간 | ISO_8601 간격은 5초에서 300초(최소 5초) 사이입니다. 기본값: 60초 |

다음 방법의 하나로 구성 옵션을 설정할 수 있습니다.

* **Azure Portal:** IoT **Hub의 허브 설정에서** **기본 제공 엔드포인트를** 선택하고 **클라우드-디바이스 메시징으로** 이동합니다. (**feedback.maxDeliveryCount** 및 **feedback.lockDurationAsIso8601** 속성 설정은 현재 Azure Portal에서 지원되지 않습니다.)

:::image type="content" source="./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png" alt-text="포털에서 클라우드-디바이스 메시지에 대한 구성 옵션 설정" border="true":::

* **Azure CLI**: [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update) 명령을 사용합니다.

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>다음 단계

클라우드-디바이스 메시지를 수신하는 데 사용할 수 있는 SDK에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

클라우드-디바이스 메시지를 수신하려면 [클라우드-디바이스 보내기](iot-hub-csharp-csharp-c2d.md) 자습서를 참조하세요.
