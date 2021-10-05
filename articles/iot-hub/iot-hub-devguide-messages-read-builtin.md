---
title: Azure IoT Hub 기본 제공 엔드포인트 이해 | Microsoft Docs
description: 개발자 가이드에서는 기본 제공 Event Hub 호환 엔드포인트를 사용하여 디바이스-클라우드 메시지를 읽는 방법을 설명합니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/16/2021
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 1a3bae398a5991bbaf79e362e5ebb3942fd2f53e
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458608"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>기본 제공 엔드포인트에서 디바이스-클라우드 메시지 읽기

기본적으로 메시지는 [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)와 호환되는 기본 제공 서비스 연결 엔드포인트(**messages/events**)로 라우팅됩니다. 이 엔드포인트는 현재 포트 5671의 [AMQP](https://www.amqp.org/) 프로토콜과 포트 443의 [WebSockets를 통한 AMQP](http://docs.oasis-open.org/amqp-bindmap/amqp-wsb/v1.0/cs01/amqp-wsb-v1.0-cs01.html)만 사용하여 노출됩니다. IoT Hub는 다음 속성을 노출하여 기본 제공 Event Hub와 호환되는 메시징 엔드포인트 **messages/events** 를 제어할 수 있게 합니다.

| 속성            | Description |
| ------------------- | ----------- |
| **분할 개수** | 이 속성은 생성 시 설정하여 디바이스-클라우드 이벤트 수집에 대한 [파티션](../event-hubs/event-hubs-features.md#partitions) 수를 정의합니다. |
| **보존 시간**  | 이 속성은 IoT Hub에서 메시지를 보존할 일 수를 지정합니다. 기본값은 1일이지만 7일로 늘릴 수 있습니다. |

IoT Hub를 사용하면 기본 제공 Event Hubs에서 데이터를 최대 7일 동안 보존할 수 있습니다. IoT Hub를 만드는 중에 보존 시간을 설정할 수 있습니다. IoT Hub의 데이터 보존 시간은 IoT 허브 계층 및 단위 유형에 따라 달라집니다. 크기 측면에서 기본 제공 Event Hubs는 최대 메시지 크기의 메시지를 최소 24시간의 할당량까지 유지할 수 있습니다. 예를 들어 1 S1 단위의 IoT Hub는 각각 4k 크기인 메시지를 400K 이상 보존할 수 있는 충분한 스토리지를 제공합니다. 디바이스에서 더 적은 수의 메시지를 보내는 경우 이러한 메시지는 사용되는 스토리지 양에 따라 최대 7일 동안 보존할 수 있습니다. 데이터를 지정된 보존 시간 동안 최소한으로 보존하도록 보장됩니다. 메시지는 만료되며, 보존 시간이 지나면 액세스할 수 없습니다. 

또한 IoT Hub를 사용하면 기본 제공 디바이스-클라우드 수신 엔드포인트에서 소비자 그룹을 관리할 수 있습니다. 각 IoT Hub당 최대 20개의 소비자 그룹이 있을 수 있습니다.

[메시지 라우팅](iot-hub-devguide-messages-d2c.md)을 사용하고 [대체 경로](iot-hub-devguide-messages-d2c.md#fallback-route)를 사용하도록 설정된 경우 경로에 대한 쿼리와 일치하지 않는 모든 메시지가 기본 제공 엔드포인트로 이동합니다. 이 대체 경로를 사용하지 않도록 설정하면 쿼리와 일치하지 않는 메시지가 삭제됩니다.

[IoT Hub 리소스 공급자 REST API](/rest/api/iothub/iothubresource)를 사용하여 또는 [Azure Portal](https://portal.azure.com)을 사용하여 프로그래밍 방식으로 보존 시간을 수정할 수 있습니다.

IoT Hub는 허브에서 수신한 디바이스-클라우드 메시지를 읽도록 백 엔드 서비스의 기본 제공 엔드포인트인 **messages/events** 를 공개합니다. 이 엔드포인트는 Event Hub와 호환되기 때문에 Event Hubs 서비스가 메시지 읽기에 대해 지원하는 모든 메커니즘을 사용할 수 있습니다.

## <a name="read-from-the-built-in-endpoint"></a>기본 제공 엔드포인트에서 읽기

일부 제품 통합 및 Event Hubs SDK에서는 IoT Hub를 인식하며, IoT 허브 서비스 연결 문자열을 사용하여 기본 제공 엔드포인트에 연결할 수 있습니다.

IoT Hub를 인식하지 않는 Event Hubs SDK 또는 제품 통합을 사용하는 경우 Event Hub 호환 엔드포인트 및 Event Hub 호환 이름이 필요합니다. 이러한 값은 다음과 같이 포털에서 검색할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

2. **기본 제공 엔드포인트** 를 클릭합니다.

3. **이벤트** 섹션에는 **파티션**, **Event Hub 호환 이름**, **Event Hub 호환 엔드포인트**, **보존 시간** 및 **소비자 그룹** 값이 포함되어 있습니다.

    ![디바이스-클라우드 설정](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

포털에서 Event Hub 호환 엔드포인트 필드에는 다음과 같은 전체 Event Hubs 연결 문자열이 포함되어 있습니다. **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456** 사용하는 SDK에 다른 값이 필요한 경우 다음과 같습니다.

| Name | 값 |
| ---- | ----- |
| 엔드포인트 | sb://abcd1234namespace.servicebus.windows.net/ |
| Hostname | abcd1234namespace.servicebus.windows.net |
| 네임스페이스 | abcd1234namespace |

그런 다음 위의 스크린샷에 표시된 대로 드롭다운 목록에서 공유 액세스 정책을 선택할 수 있습니다. 지정된 이벤트 허브에 연결할 수 있는 **ServiceConnect** 권한이 있는 정책만 표시됩니다.

IoT Hub에서 공개하는 기본 제공 Event Hub 호환 엔드포인트에 연결하는 데 사용할 수 있는 SDK는 다음과 같습니다.

| 언어 | SDK) | 예제 |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [빠른 시작](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [빠른 시작](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [빠른 시작](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) |
| Python | https://pypi.org/project/azure-eventhub/ | [빠른 시작](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) |

IoT Hub에서 공개하는 기본 제공 Event Hub 호환 엔드포인트에 사용할 수 있는 제품 통합은 다음과 같습니다.

* [Azure Functions](../azure-functions/index.yml). [Azure Functions에 대한 Azure IoT Hub 바인딩](../azure-functions/functions-bindings-event-iot.md)을 참조하세요.
* [Azure Stream Analytics](../stream-analytics/index.yml) - [Stream Analytics에 입력으로 데이터 스트리밍](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub) 참조
* [Time Series Insights](../time-series-insights/index.yml). [Time Series Insights 환경에 IoT 허브 이벤트 원본 추가](../time-series-insights/how-to-ingest-data-iot-hub.md) 참조
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). GitHub의 [spout 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 을 볼 수 있습니다.
* [Apache Spark 통합](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md)
* [Azure Databricks](/azure/azure-databricks/).

## <a name="use-amqp-ws-or-a-proxy-with-event-hubs-sdks"></a>AMQP-WS 또는 Event Hubs SDK와 함께 프록시 사용

Event Hubs SDK를 사용하여 WebSocket을 통한 AMQP 또는 프록시를 통한 읽기가 필요한 환경에서 기본 제공 엔드포인트에서 읽을 수 있습니다. 자세한 내용은 다음 샘플을 참조하세요.

| 언어 | 샘플 |
| -------- | ------ |
| .NET | [ReadD2cMessages .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Quickstarts/ReadD2cMessages) |
| Java | [read-d2c-messages Java](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Quickstarts/read-d2c-messages) |
| Node.js | [read-d2c-messages Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/iot-hub/Quickstarts/read-d2c-messages) |
| Python | [read-dec-messages Python](https://github.com/Azure-Samples/azure-iot-samples-python/tree/master/iot-hub/Quickstarts/read-d2c-messages) |

## <a name="next-steps"></a>다음 단계

* IoT Hub 엔드포인트에 대한 자세한 내용은 [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)를 참조하세요.

* [빠른 시작](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)에서는 시뮬레이트된 디바이스에서 디바이스-클라우드 메시지를 보내고 기본 제공 엔드포인트에서 메시지를 읽는 방법을 보여 줍니다. 

자세한 내용은 [경로를 사용하여 IoT Hub 디바이스-클라우드 메시지 처리](tutorial-routing.md) 자습서를 참조하세요.

* 디바이스-클라우드 메시지를 사용자 지정 엔드포인트로 라우팅하려면 [디바이스-클라우드 메시지에 대해 메시지 라우팅 및 사용자 지정 엔드포인트 사용](iot-hub-devguide-messages-read-custom.md)을 참조하세요.