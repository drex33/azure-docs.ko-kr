---
title: Azure IoT Hub 메시지 라우팅 이해하기 | Microsoft Docs
description: 개발자 가이드 - 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 보내는 방법입니다. 원격 분석 데이터 및 비원격 분석 데이터를 전송하는 방법에 대한 정보가 포함됩니다.
author: nehsin
manager: mehmet.kucukgoz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/14/2021
ms.author: nehsin
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: 5a405c50c94a16394a92ba7c4830ab3f3aee072f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449719"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트에 보내기

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

메시지 라우팅을 사용하면 확장 가능하고 신뢰할 수 있는 자동화된 방식으로 디바이스에서 클라우드 서비스로 메시지를 보낼 수 있습니다. 메시지 라우팅은 다음 용도로 사용할 수 있습니다. 

* **디바이스 원격 분석 메시지는 물론 이벤트** 즉, 디바이스 수명 주기 이벤트, 디바이스 쌍 변경 이벤트, 디지털 트윈 변경 이벤트 및 디바이스 연결 상태 이벤트를 기본 제공 엔드포인트 및 사용자 지정 엔드포인트로 보냅니다. [엔드포인트 라우팅](#routing-endpoints)에 대해 알아보세요. IoT 플러그 앤 플레이 디바이스에서 전송되는 이벤트에 대해 자세히 알아보려면 [IoT 플러그 앤 플레이 디지털 트윈 이해](../iot-develop/concepts-digital-twin.md)를 참조하세요.

* 다양한 쿼리를 적용하여 **다양한 엔드포인트로 데이터를 라우팅하기 전에 데이터를 필터링** 합니다. 메시지 라우팅을 사용하면 메시지 속성 및 메시지 본문뿐만 아니라 디바이스 쌍 태그 및 디바이스 쌍 속성에 대해서도 쿼리할 수 있습니다. [메시지 라우팅에 쿼리](iot-hub-devguide-routing-query-syntax.md)를 사용하는 방법에 대해 자세히 알아보세요.

IoT Hub는 메시지 라우팅을 작동하기 위해 이러한 서비스 엔드포인트에 대한 쓰기 액세스가 필요합니다. Azure Portal을 통해 엔드포인트를 구성하는 경우 필요한 권한이 추가됩니다. 예상된 처리량을 지원하도록 서비스를 구성해야 합니다. 예를 들어 Event Hubs를 사용자 지정 엔드포인트로 사용하는 경우 IoT Hub 메시지 라우팅을 통해 전송하려는 이벤트의 수신을 처리할 수 있도록 해당 이벤트 허브에 대한 **처리량 단위** 를 구성해야 합니다. 마찬가지로 Service Bus 큐를 엔드포인트로 사용하는 경우에는 소비자가 송신할 때까지 큐가 수신된 모든 데이터를 보유할 수 있도록 **최대 크기** 를 구성해야 합니다. IoT 솔루션을 처음 구성할 때 다른 끝점을 모니터링 하 고 실제 부하를 위해 필요한 사항을 조정 해야 할 수 있습니다.

IoT Hub는 프로토콜 전체에서의 상호 운용성을 위해 모든 디바이스-클라우드 메시징에 대해 [일반적인 형식](iot-hub-devguide-messages-construct.md)을 정의합니다. 메시지가 동일한 엔드포인트를 가리키는 여러 경로와 일치하는 경우 IoT Hub는 메시지를 해당 엔드포인트에 한 번만 전달합니다. 따라서 Service Bus 큐 또는 항목에 대해 중복 제거를 구성할 필요가 없습니다. [메시지 라우팅을 구성](tutorial-routing.md)하는 방법을 알아보려면 이 자습서를 사용하세요.

## <a name="routing-endpoints"></a>라우팅 엔드포인트

IoT Hub에는 Event Hubs와 호환되는 기본 제공 엔드포인트(**메시지/이벤트**)가 있습니다. 구독의 다른 서비스를 IoT Hub에 연결하여 메시지를 라우팅할 [사용자 지정 엔드포인트](iot-hub-devguide-endpoints.md#custom-endpoints)를 만들 수 있습니다. 

각 메시지는 일치하는 라우팅 쿼리가 있는 모든 엔드포인트로 라우팅됩니다. 즉, 메시지를 여러 엔드포인트로 라우팅할 수 있습니다.

사용자 지정 끝점에 방화벽 구성이 있는 경우 [Microsoft 신뢰할 수 있는 자사 예외](./virtual-network-support.md#egress-connectivity-from-iot-hub-to-other-azure-resources) 를 사용 하는 것이 좋습니다.

IoT Hub는 현재 다음과 같은 엔드포인트를 지원합니다.

 - 기본 제공 엔드포인트
 - Azure Storage
 - Service Bus 큐 및 Service Bus 토픽
 - Event Hubs

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>라우팅 엔드포인트로 기본 제공 엔드포인트

표준 [Event Hubs 통합 및 SDK](iot-hub-devguide-messages-read-builtin.md)를 사용하여 기본 제공 엔드포인트(**메시지/이벤트**)에서 디바이스-클라우드 메시지를 수신할 수 있습니다. 일단 경로가 생성되면 경로가 해당 엔드포인트에 대해 생성되어 있지 않은 한, 데이터가 기본 제공 엔드포인트로 흐르는 것을 멈춥니다. 경로를 만들지 않은 경우에도 기본 제공 끝점으로 메시지를 라우팅하도록 대체 경로를 사용 하도록 설정 해야 합니다. Portal 또는 CLI를 사용 하 여 허브를 만드는 경우에는 기본적으로 대체 (fallback)가 사용 됩니다.

## <a name="azure-storage-as-a-routing-endpoint"></a>라우팅 엔드포인트로 Azure Storage

[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) 및 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2) 계정으로 메시지를 라우팅할 수 IoT Hub 두 개의 저장소 서비스가 있습니다. Azure Data Lake Storage 계정은 Blob 스토리지를 기반으로 구축된 [계층적 네임스페이스](../storage/blobs/data-lake-storage-namespace.md)사용 스토리지 계정입니다. 두 가지 모두 해당 스토리지에 대해 Blob을 사용합니다.

IoT Hub [Apache Avro](https://avro.apache.org/) 형식 및 JSON 형식의 Azure Storage에 데이터를 쓸 수 있도록 지원 합니다. 기본값은 AVRO입니다. JSON 인코딩을 사용하는 경우에는 메시지 [시스템 속성](iot-hub-devguide-routing-query-syntax.md#system-properties)에서 contentType을 **application/json** 으로 설정하고 contentEncoding을 **UTF-8** 로 설정해야 합니다. 이러한 두 값은 대/소문자를 구분하지 않습니다. 콘텐츠 인코딩을 지정하지 않으면 IoT Hub는 base 64 인코딩 형식으로 메시지를 작성합니다.

인코딩 형식은 Blob 스토리지 엔드포인트가 구성된 경우에만 설정할 수 있습니다. 기존 엔드포인트에 대해서는 편집할 수 없습니다. 기존 끝점에 대 한 인코딩 형식을 전환 하려면 end endoint을 삭제 하 고 원하는 형식으로 다시 만들어야 합니다. 유용한 전략 중 하나는 원하는 인코딩 형식을 사용하여 새 사용자 지정 엔드포인트를 만들고 해당 엔드포인트에 병렬 경로를 추가하는 것입니다. 이러한 방식으로 기존 끝점을 삭제 하기 전에 데이터를 확인할 수 있습니다.

IoT Hub 만들기 또는 업데이트 REST API, 특히 [RoutingStorageContainerProperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/iot/hub/routing-endpoint) 또는 [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint)을 사용하여 인코딩 형식을 선택할 수 있습니다. 다음 이미지는 Azure Portal에서 인코딩 형식을 선택하는 방법을 보여 줍니다.

![Blob 스토리지 엔드포인트 인코딩](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub는 메시지를 일괄 처리하고, 일괄 처리가 특정 크기에 도달하거나 일정 시간이 경과할 때마다 스토리지에 데이터를 씁니다. IoT Hub는 기본적으로 다음 파일 명명 규칙을 따릅니다.

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

원하는 파일 명명 규칙을 사용할 수 있지만, 나열된 토큰은 모두 사용해야 합니다. 쓸 데이터가 없으면 IoT Hub가 빈 Blob을 작성합니다.

Blob 또는 파일을 나열한 다음, 이를 반복하여 파티션을 가정하지 않고 모든 Blob 또는 파일을 읽을 수 있도록 하는 것이 좋습니다. 파티션 범위는 [Microsoft 시작 장애 조치(failover)](iot-hub-ha-dr.md#microsoft-initiated-failover) 또는 IoT Hub [수동 장애 조치(failover)](iot-hub-ha-dr.md#manual-failover) 중에 변경할 수 있습니다. Blob 목록을 열거하기 위해 [Blob API 나열](/rest/api/storageservices/list-blobs)을 사용하거나 파일 목록에 대해 [ADLS Gen2 API 나열](/rest/api/storageservices/datalakestoragegen2/path)을 사용할 수 있습니다. 지침으로 다음 샘플을 참조 하세요.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

Azure Data Lake Gen2 호환 스토리지 계정을 만들려면 새 V2 스토리지 계정을 만들고 다음 이미지와 같이 **고급** 탭의 *계층 구조 네임스페이스* 필드에서 *사용* 을 선택합니다.

![Azure Date Lake Gen2 스토리지 선택](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>라우팅 엔드포인트로 Service Bus 큐 및 Service Bus 토픽

IoT Hub으로 사용되는 Service Bus 큐 및 토픽에는 **세션** 또는 **중복 검색** 이 사용하도록 설정되어 있어서는 안 됩니다. 두 옵션 중 하나가 사용하도록 설정되어 있으면 Azure Portal에서 엔드포인트가 **연결할 수 없음** 으로 표시됩니다.

## <a name="event-hubs-as-a-routing-endpoint"></a>라우팅 엔드포인트로 Event Hubs

기본 제공 Event Hubs 호환 엔드포인트 외에, Event Hubs 유형의 사용자 지정 엔드포인트로 데이터를 라우팅할 수도 있습니다. 

## <a name="reading-data-that-has-been-routed"></a>라우팅된 데이터 읽기

이 [자습서](tutorial-routing.md)에 따라 경로를 구성할 수 있습니다.

다음 자습서를 사용하여 엔드포인트에서 메시지를 읽는 방법을 알아봅니다.

* [기본 제공 엔드포인트](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)에서 읽기

* [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)에서 읽기

* [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)에서 읽기

* [Service Bus 큐](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)에서 읽기

* [Service Bus 토픽](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)에서 읽기


## <a name="fallback-route"></a>대체(fallback) 경로

대체(fallback) 경로는 기존 경로에서 쿼리 조건을 충족하지 않는 모든 메시지를 [Event Hubs](../event-hubs/index.yml)와 호환되는 기본 제공 Event Hubs(**messages/events**)로 보냅니다. 메시지 라우팅이 설정되어 있으면 대체 경로 기능을 사용하도록 설정할 수 있습니다. 일단 경로가 생성되면 경로가 해당 엔드포인트에 대해 생성되어 있지 않은 한, 데이터가 기본 제공 엔드포인트로 흐르는 것을 멈춥니다. 기본 제공 엔드포인트에 대한 경로가 없고 대체(fallback) 경로를 사용할 수 있는 경우 경로의 모든 쿼리 조건을 충족하지 않는 메시지만 기본 제공 엔드포인트로 전송됩니다. 또한 기존 경로가 모두 삭제된 경우에는 대체(fallback) 경로가 기본 제공 엔드포인트에서 모든 데이터를 수신하도록 설정해야 합니다. 

대체 경로는 Azure Portal->메시지 라우팅 블레이드에서 사용하도록/사용하지 않도록 설정할 수 있습니다. [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties)에 Azure Resource Manager를 사용하여 대체(fallback) 경로에 대해 사용자 지정 엔드포인트를 사용할 수도 있습니다.

## <a name="non-telemetry-events"></a>비-원격 분석 이벤트

장치 원격 분석 외에도 메시지 라우팅을 통해 장치 쌍 변경 이벤트, 장치 수명 주기 이벤트, 디지털 쌍 변경 이벤트 및 장치 연결 상태 이벤트를 보낼 수 있습니다. 예를 들어, **디바이스 쌍 변경 이벤트** 로 설정된 데이터 원본으로 경로가 생성되면, IoT Hub는 디바이스 쌍의 변경 사항을 포함하는 엔드포인트로 메시지를 전송합니다. 마찬가지로 **디바이스 수명 주기 이벤트** 로 설정된 데이터 원본으로 경로가 만들어지면 IoT Hub는 디바이스가 삭제되었거나 생성되었는지 여부를 나타내는 메시지를 보냅니다. 개발자는 [Azure IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md)의 일부로 **디지털 트윈 변경 이벤트** 로 설정된 데이터 원본이 있는 경로를 만들 수 있으며 IoT Hub는 디지털 트윈 속성이 설정되거나 변경되고, 디지털 트윈이 대체되거나 기본 디바이스 쌍에 대해 변경 이벤트가 발생하는 경우 메시지를 전송합니다. 마지막으로 데이터 원본이 **디바이스 연결 상태 이벤트** 로 설정된 경로가 생성되면 IoT Hub는 디바이스가 연결되었는지 여부를 나타내는 메시지를 보냅니다.


[IoT Hub는 Azure Event Grid와 통합](iot-hub-event-grid.md)되어 디바이스 이벤트를 게시하여 이러한 이벤트를 기반으로 워크플로의 실시간 통합 및 자동화를 지원합니다. [메시지 라우팅과 Event Grid 간의 주요 차이점](iot-hub-event-grid-routing-comparison.md)을 확인하고 내 시나리오에 무엇이 가장 적합한지 알아보세요.

## <a name="limitations-for-device-connection-state-events"></a>디바이스 연결 상태 이벤트에 대한 제한 사항

디바이스 연결 상태 이벤트를 수신하려면 디바이스가 IoT Hub를 사용하여 *디바이스-클라우드 전송 원격 분석* 또는 *클라우드-디바이스 수신 메시지* 작업을 호출해야 합니다. 그러나 디바이스가 AMQP 프로토콜을 사용하여 IoT Hub에 연결하는 경우 디바이스가 *클라우드-디바이스 수신 메시지* 작업을 호출하는 것이 좋습니다. 그러지 않으면 연결 상태 알림이 몇 분 지연될 수 있습니다. 디바이스가 MQTT 프로토콜로 연결되는 경우 IoT Hub는 클라우드-디바이스 링크를 열린 상태로 유지합니다. AMQP에 대한 클라우드-디바이스 링크를 열려면 [수신 비동기 API](/rest/api/iothub/device/receivedeviceboundnotification)를 호출합니다.

디바이스-클라우드 링크는 디바이스에서 원격 분석을 전송하는 동안 열린 상태로 유지됩니다.

장치 연결이 깜박이는 경우, 즉 장치가 연결 하 고 연결이 끊어지는 경우에는 IoT Hub 모든 단일 연결 상태를 전송 하지는 않지만 깜박임이 중지 될 때까지 60 초의 주기적인 스냅숏에서 수행 된 현재 연결 상태를 게시 합니다. 동일한 연결 상태 이벤트를 여러 시퀀스 번호로 수신하거나 여러 연결 상태 이벤트를 수신하는 경우 디바이스 연결 상태가 변경된 것을 의미합니다.

## <a name="testing-routes"></a>경로 테스트

새 경로를 만들거나 기존 경로를 편집하는 경우에는 샘플 메시지로 경로 쿼리를 테스트해야 합니다. 개별 경로를 테스트하거나 모든 경로를 한 번에 테스트할 수 있으며 테스트 중에는 엔드포인트에 메시지가 라우팅되지 않습니다. Azure Portal, Azure Resource Manager, Azure PowerShell 및 Azure CLI를 테스트에 사용할 수 있습니다. 결과는 샘플 메시지가 쿼리와 일치하는지, 메시지가 쿼리와 일치하지 않는지 또는 샘플 메시지나 쿼리 구문이 올바르지 않아서 테스트를 실행할 수 없는지 여부를 식별하는 데 도움이 됩니다. 자세히 알아보려면 [경로 테스트](/rest/api/iothub/iothubresource/testroute) 및 [모든 경로 테스트](/rest/api/iothub/iothubresource/testallroutes)를 참조하세요.

## <a name="latency"></a>대기 시간

기본 제공 엔드포인트를 사용하여 디바이스-클라우드 간 원격 분석 메시지를 라우팅할 경우 첫 번째 경로를 만든 후 엔드투엔드 대기 시간이 약간 증가합니다.

대부분의 경우 평균적인 대기 시간 증가는 500ms 미만입니다. **라우팅: 메시지/이벤트에 대한 메시지 대기 시간** 또는 **d2c.endpoints.latency.builtIn.events** IoT Hub 메트릭을 사용하여 대기 시간을 모니터링할 수 있습니다. 첫 번째 경로 이후에 다른 경로를 만들거나 삭제해도 엔드투엔드 대기 시간에는 영향을 주지 않습니다.

## <a name="monitoring-and-troubleshooting"></a>모니터링 및 문제 해결

IoT Hub는 허브 및 메시지 전송 상태에 대한 개요를 제공하는 라우팅 및 엔드포인트와 관련된 여러 가지 메트릭을 제공합니다. 기능 범주별로 분류된 모든 IoT Hub 메트릭의 목록은 [데이터 참조 모니터링의 메트릭](monitor-iot-hub-reference.md#metrics)을 참조하세요. 라우팅 쿼리 및 엔드포인트 상태를 평가하는 동안 발생하는 오류는 [IoT Hub 리소스 로그의 **경로** 범주](monitor-iot-hub-reference.md#routes)를 사용하여 IoT Hub에 의해 인식되는 것으로 추적할 수 있습니다. IoT Hub에서 메트릭 및 리소스 로그를 사용하는 방법에 대한 자세한 내용은 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요.

REST API [Endpoint Health 가져오기](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 사용 하 여 Endpoint의 [Health 상태](iot-hub-devguide-endpoints.md#custom-endpoints) 를 가져올 수 있습니다.

라우팅 문제 해결에 대한 자세한 내용과 지원 정보는 [라우팅에 대한 문제 해결 가이드](troubleshoot-message-routing.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 메시지 경로를 만드는 방법에 대한 자세한 내용은 [경로를 사용하여 IoT Hub 디바이스-클라우드 메시지 처리](tutorial-routing.md)를 참조하세요.

* [디바이스-클라우드 메시지를 보내는 방법](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)

* 디바이스-클라우드 메시지를 보내는 데 사용할 수 있는 SDK에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.