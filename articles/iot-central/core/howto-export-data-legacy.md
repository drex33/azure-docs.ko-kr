---
title: Azure IoT Central에서 데이터 내보내기(레거시) | Microsoft Docs
description: Azure IoT Central 애플리케이션에서 Azure Event Hubs, Azure Service Bus, Azure Blob Storage로 데이터를 내보내는 방법
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: cd4df6157d4b163a6c475b0f627a1d96466f8ae5
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835817"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>데이터 내보내기(레거시)를 사용하여 IoT 데이터를 클라우드 대상으로 내보내기

> [!Note]
> 이 문서에서는 IoT Central의 레거시 데이터 내보내기 기능을 설명합니다.
>
> - 최신 데이터 내보내기 기능에 관한 정보는 [데이터 내보내기를 사용하여 IoT 데이터를 클라우드 대상으로 내보내기](./howto-export-data.md)를 참조하세요.
> - 미리 보기 데이터 내보내기와 레거시 데이터 내보내기 기능의 차이점에 관해 알아보려면 [비교 테이블](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export)을 참조하세요.

이 문서에서는 Azure IoT Central에서 데이터 내보내기 기능을 사용하는 방법을 설명합니다. 이 기능을 사용하면 **Azure Event Hubs**, **Azure Service Bus** 또는 **Azure Blob Storage** 인스턴스로 데이터를 지속적으로 내보낼 수 있습니다. 데이터 내보내기는 JSON 형식을 사용하며 원격 분석, 디바이스 정보, 디바이스 템플릿 정보를 포함할 수 있습니다. 다음에 대해 내보낸 데이터를 사용합니다.

- 웜 경로 인사이트 및 분석. 이 옵션에는 Azure Stream Analytics에서 사용자 지정 규칙을 트리거하거나, Azure Logic Apps에서 사용자 지정 워크플로를 트리거하거나, Azure Functions를 통해 변환할 데이터를 전달하는 작업이 포함됩니다.
- Azure Machine Learning의 학습 모델이나 Microsoft Power BI의 장기 추세 분석과 같은 콜드 경로 분석.

> [!Note]
> 데이터 내보내기를 켜면 그 시점 이후의 데이터만 가져옵니다. 현재 데이터 내보내기가 꺼져 있는 동안에는 데이터를 검색할 수 없습니다. 더 많은 기록 데이터를 유지하려면 데이터 내보내기를 초기에 켜세요.

## <a name="prerequisites"></a>사전 요구 사항

IoT Central 애플리케이션의 관리자이거나 데이터 내보내기 권한이 있어야 합니다.

## <a name="set-up-export-destination"></a>내보내기 대상 설정

데이터 내보내기를 구성하려면 먼저 내보내기 대상이 있어야 합니다.

### <a name="create-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

내보낼 기존 Event Hubs 네임스페이스가 없는 경우 다음 단계를 수행합니다.

1. [Azure Portal에서 새 Event Hubs 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.EventHub)를 만듭니다. [Azure Event Hubs 문서](../../event-hubs/event-hubs-create.md)에서 자세히 알아볼 수 있습니다.

2. 구독을 선택합니다. IoT Central 애플리케이션과 동일한 구독에 있는 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용하여 연결합니다.

3. Event Hubs 네임스페이스에서 이벤트 허브를 만듭니다. 네임스페이스로 이동한 다음, 맨 위에서 **+ 이벤트 허브** 를 선택하여 이벤트 허브 인스턴스를 만듭니다.

### <a name="create-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

내보낼 기존 Service Bus 네임스페이스가 없는 경우 다음 단계를 수행합니다.

1. [Azure Portal에서 새 Service Bus 네임스페이스](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)를 만듭니다. [Azure Service Bus 문서](../../service-bus-messaging/service-bus-create-namespace-portal.md)에서 자세히 알아볼 수 있습니다.
2. 구독을 선택합니다. IoT Central 애플리케이션과 동일한 구독에 있는 다른 구독으로 데이터를 내보낼 수 있습니다. 이 경우 연결 문자열을 사용하여 연결합니다.

3. 내보낼 큐 또는 토픽을 만들려면 Service Bus 네임스페이스로 이동하고, **+ 큐** 또는 **+ 토픽** 을 선택합니다.

Service Bus를 내보내기 대상으로 선택할 경우 큐와 토픽에서 세션 또는 중복 검색을 사용하도록 설정하지 않아야 합니다. 이러한 옵션 중 하나가 사용하도록 설정되면 일부 메시지가 큐 또는 토픽에 도착하지 않습니다.

### <a name="create-storage-account"></a>스토리지 계정 만들기

내보낼 기존 Azure 스토리지 계정이 없는 경우 다음 단계를 수행합니다.

1. [Azure Portal에서 새 스토리지 계정](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)을 만듭니다. 새 [Azure Blob Storage 계정](../../storage/blobs/storage-quickstart-blobs-portal.md) 또는 [Azure Data Lake Storage v2 스토리지 계정](../../storage/common/storage-account-create.md)을 만드는 방법에 대해 자세히 알아볼 수 있습니다. 데이터 내보내기는 블록 Blob을 지원하는 스토리지 계정에만 데이터를 쓸 수 있습니다. 다음 목록에는 알려진 호환되는 스토리지 계정 유형이 나와 있습니다.

    |성능 계층|계정 유형|
    |-|-|
    |Standard|범용 V2|
    |Standard|범용 V1|
    |Standard|Blob Storage|
    |Premium|블록 Blob Storage|

2. 스토리지 계정에 컨테이너를 만듭니다. 스토리지 계정으로 이동합니다. **Blob 서비스** 에서 **Blob 찾아보기** 를 선택합니다. 맨 위에서 **+ 컨테이너** 를 선택하여 새 컨테이너를 만듭니다.

## <a name="set-up-data-export"></a>데이터 내보내기 설정

데이터를 내보낼 대상이 있으므로 이제 다음 단계에 따라 데이터 내보내기를 설정합니다.

1. IoT Central 애플리케이션에 로그인합니다.

2. 왼쪽 창에서 **데이터 내보내기** 를 선택합니다.

    > [!Tip]
    > 왼쪽 창에 **데이터 내보내기** 가 표시되지 않으면 앱에서 데이터 내보내기를 구성할 수 있는 권한이 없는 것입니다. 관리자에게 데이터 내보내기를 설정하도록 요청합니다.

3. **+새로 만들기** 단추를 선택합니다. **Azure Blob Storage**, **Azure Event Hubs**, **Azure Service Bus 큐** 또는 **Azure Service Bus 토픽** 중 하나를 내보내기 대상으로 선택합니다. 애플리케이션당 최대 내보내기 수는 5개입니다.

4. 내보내기의 이름을 입력합니다. 드롭다운 목록 상자에서 **네임스페이스** 또는 **연결 문자열 입력** 을 선택합니다.

    - IoT Central 애플리케이션과 동일한 구독에 있는 스토리지 계정, Event Hubs 네임스페이스, Service Bus 네임스페이스만 표시됩니다. 이 구독 외부의 대상으로 내보내려면 **연결 문자열 입력** 을 선택하고 6단계를 참조합니다.
    - 무료 가격 책정 플랜을 사용하여 만든 앱의 경우 데이터 내보내기를 구성하는 유일한 방법은 연결 문자열을 사용하는 것입니다. 무료 가격 책정 플랜의 앱에는 연결된 Azure 구독이 없습니다.

    ![새 이벤트 허브 만들기](media/howto-export-data-legacy/export-event-hub.png)

5. 드롭다운 목록 상자에서 이벤트 허브, 큐, 토픽 또는 컨테이너를 선택합니다.

6. (선택 사항) **연결 문자열 입력** 을 선택한 경우 연결 문자열을 붙여넣을 수 있는 새 상자가 나타납니다. 다음 항목의 연결 문자열을 가져오려면

    - Event Hubs 또는 Service Bus의 경우 Azure Portal에서 네임스페이스로 이동합니다.
        - 전체 네임스페이스에 연결 문자열을 사용하려면 다음을 수행합니다.
            1. **설정** 아래에서 **공유 액세스 정책** 을 선택합니다.
            2. 새 키를 만들거나 **보내기** 권한이 있는 기존 키를 선택합니다.
            3. 주 또는 보조 연결 문자열 중 하나를 복사합니다.
        - 특정 이벤트 허브 인스턴스나 Service Bus 큐 또는 토픽에 연결 문자열을 사용하려면 **엔터티 > Event Hubs**, **엔터티 > 큐** 또는 **엔터티 > 토픽** 으로 이동합니다. 특정 인스턴스를 선택하고 위의 동일한 단계에 따라 연결 문자열을 가져옵니다.
    - 스토리지 계정의 경우 Azure Portal에서 스토리지 계정으로 이동합니다.
        - 전체 스토리지 계정의 연결 문자열만 지원됩니다. 단일 컨테이너로 범위가 지정된 연결 문자열은 지원되지 않습니다.
          1. **설정** 에서 **액세스 키** 를 선택합니다.
          2. key1 연결 문자열 또는 key2 연결 문자열 중 하나를 복사합니다.

    연결 문자열에 붙여넣습니다. 인스턴스 또는 대/소문자를 구분하는 **컨테이너 이름** 을 입력합니다.

7. **내보낼 데이터** 에서 형식을 **켜기** 로 설정하여 내보낼 데이터 형식을 선택합니다.

8. 데이터 내보내기를 켜려면 **사용** 토글이 **켜기** 인지 확인합니다. **저장** 을 선택합니다.

9. 몇 분 후에 데이터가 선택한 대상에 표시됩니다.

## <a name="export-contents-and-format"></a>내보내기 콘텐츠 및 형식

내보낸 원격 분석 데이터에는 원본 분석 값 자체만이 아니라 디바이스가 IoT Central에 보낸 전체 메시지가 포함됩니다. 내보낸 디바이스 데이터에는 모든 디바이스에 대한 속성 및 메타데이터의 변경 내용이 포함되고, 내보낸 디바이스 템플릿에는 모든 디바이스 템플릿의 변경 내용이 포함됩니다.

Event Hubs와 Service Bus의 경우 데이터를 거의 실시간으로 내보냅니다. 데이터는 `body` 속성에 있고 JSON 형식입니다. 아래에서 예제를 참조하세요.

Blob Storage의 경우 데이터는 분당 한 번씩 내보내지며, 각 파일에는 마지막으로 내보낸 파일 이후 변경 내용의 일괄 처리가 포함됩니다. 내보낸 데이터는 JSON 형식으로 세 개의 폴더에 배치됩니다. 스토리지 계정의 기본 경로는 다음과 같습니다.

- 원격 분석: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 디바이스: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 디바이스 템플릿: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Azure Portal에서 내보낸 파일을 찾아보려면 해당 파일로 이동하고 **Blob 편집** 탭을 선택합니다.

## <a name="telemetry"></a>원격 분석

Event Hubs 및 Service Bus의 경우 IoT Central은 디바이스로부터 메시지를 받은 후 새 메시지를 빠르게 내보냅니다. 각 내보낸 메시지에는 JSON 형식의 body 속성으로 디바이스가 보낸 전체 메시지가 포함됩니다.

Blob Storage의 경우 메시지는 1분에 한 번씩 일괄 처리하여 내보냅니다. 내보낸 파일은 [IoT Hub 메시지 라우팅](../../iot-hub/tutorial-routing.md)에서 Blob Storage로 내보낸 메시지 파일과 동일한 형식을 사용합니다.

> [!NOTE]
> Blob Storage의 경우 디바이스가 `contentType: application/JSON` 및 `contentEncoding:utf-8`(또는 `utf-16`, `utf-32`)가 포함된 메시지를 보내고 있는지 확인합니다. 예제는 [IoT Hub 설명서](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)를 참조하세요.

원격 분석을 보낸 디바이스는 디바이스 ID로 표시됩니다(다음 섹션 참조). 디바이스의 이름을 가져오려면 디바이스 데이터를 내보내고 디바이스 메시지의 **deviceId** 와 일치하는 **connectionDeviceId** 를 사용하여 각 메시지에 상관 관계를 지정합니다.

다음 예제에서는 이벤트 허브나 Service Bus 큐 또는 토픽에서 받은 메시지를 보여 줍니다.

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

이 메시지에는 보내는 디바이스의 디바이스 ID가 포함되지 않습니다.

Azure Stream Analytics 쿼리의 메시지 데이터에서 디바이스 ID를 검색하려면 [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue) 함수를 사용합니다. 예제를 보려면 [Stream Analytics, Azure Functions 및 SendGrid를 사용하여 사용자 지정 규칙으로 Azure IoT Central 확장](./howto-create-custom-rules.md)의 쿼리를 참조하세요.

Azure Databricks 또는 Apache Spark 작업 영역에서 디바이스 ID를 검색하려면 [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)를 사용합니다. 예제를 보려면 [Azure Databricks를 사용하여 사용자 지정 분석으로 Azure IoT Central 확장](./howto-create-custom-analytics.md)의 Databricks 작업 영역을 참조하세요.

다음 예제에서는 Blob Storage로 내보낸 레코드를 보여 줍니다.

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>디바이스

스냅샷의 각 메시지나 레코드는 마지막으로 내보낸 메시지 이후 디바이스와 해당 디바이스 및 클라우드 속성에 대한 하나 이상의 변경 내용을 나타냅니다. 메시지에는 다음이 포함됩니다.

- IoT Central의 디바이스 `id`
- 디바이스의 `displayName`
- `instanceOf`의 디바이스 템플릿 ID
- `simulated` 플래그, 디바이스가 시뮬레이션된 디바이스인 경우 true
- `provisioned` 플래그, 디바이스가 프로비저닝된 경우 true
- `approved` 플래그, 디바이스가 데이터를 보내도록 승인된 경우 true
- 속성 값
- 디바이스 및 클라우드 속성 값을 포함하는 `properties`

삭제된 디바이스는 내보내지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스에 대한 표시기가 없습니다.

Event Hubs와 Service Bus의 경우 IoT Central은 디바이스 데이터가 포함된 메시지를 거의 실시간으로 이벤트 허브나 Service Bus 큐 또는 토픽으로 보냅니다.

Blob Storage의 경우 마지막으로 기록된 변경 내용 이후 모든 변경 내용을 포함하는 새 스냅샷을 분당 한 번 내보냅니다.

다음 예제 메시지는 이벤트 허브나 Service Bus 큐 또는 토픽의 디바이스 및 속성 데이터에 관한 정보를 보여 줍니다.

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

이 스냅샷은 Blob Storage에 있는 디바이스 및 속성 데이터를 보여 주는 예제 메시지입니다. 내보낸 파일에는 레코드당 한 줄이 포함됩니다.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>디바이스 템플릿

각 메시지나 스냅샷 레코드는 마지막으로 내보낸 메시지 이후 게시된 디바이스 템플릿에 대한 하나 이상의 변경 내용을 나타냅니다. 각 메시지나 레코드로 보낸 정보에는 다음이 포함됩니다.

- 위 디바이스 스트림의 `instanceOf`와 일치하는 디바이스 템플릿의 `id`
- 디바이스 템플릿의 `displayName`
- `interfaces`와 원격 분석, 속성 및 명령 정의를 포함하는 디바이스 `capabilityModel`
- `cloudProperties` 정의
- `capabilityModel`과 인라인인 재정의 및 초기 값

삭제된 디바이스 템플릿은 내보내지 않습니다. 현재 내보낸 메시지에는 삭제된 디바이스 템플릿에 대한 표시기가 없습니다.

Event Hubs와 Service Bus의 경우 IoT Central은 디바이스 템플릿 데이터가 포함된 메시지를 거의 실시간으로 이벤트 허브나 Service Bus 큐 또는 토픽으로 보냅니다.

Blob Storage의 경우 마지막으로 기록된 변경 내용 이후 모든 변경 내용을 포함하는 새 스냅샷을 분당 한 번 내보냅니다.

이 예제에서는 이벤트 허브나 Service Bus 큐 또는 토픽에 있는 디바이스 템플릿 데이터에 관한 메시지를 보여 줍니다.

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

이 예제 스냅샷은 Blob Storage의 디바이스 및 속성 데이터를 포함하는 메시지를 보여 줍니다. 내보낸 파일에는 레코드당 한 줄이 포함됩니다.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>데이터 형식 변경 알림

> [!Note]
> 원격 분석 스트림 데이터 형식은 이 변경의 영향을 받지 않습니다. 데이터의 디바이스 및 디바이스 템플릿에 영향을 줍니다.

‘디바이스’ 및 ‘디바이스 템플릿’ 스트림이 켜진 미리 보기 애플리케이션에 기존 데이터 내보내기가 있는 경우 **2020년 6월 30일** 까지 내보내기를 업데이트합니다.  이 요구 사항은 Azure Blob Storage, Azure Event Hubs 및 Azure Service Bus에 대한 내보내기에 적용됩니다.

2020년 2월 3일부터 디바이스 및 디바이스 템플릿이 사용하도록 설정된 애플리케이션의 모든 새 내보내기는 위에서 설명한 데이터 형식을 사용합니다. 이 날짜 전에 만든 모든 내보내기는 2020년 6월 30일까지 이전 데이터 형식으로 유지되며, 해당 날짜에 해당 내보내기는 자동으로 새 데이터 형식으로 마이그레이션됩니다. 새 데이터 형식은 IoT Central 퍼블릭 API의 [디바이스](/rest/api/iotcentral/1.0/devices/get), [디바이스 속성](/rest/api/iotcentral/1.0/devices/get-properties) 및 [디바이스 템플릿](/rest/api/iotcentral/1.0/device-templates/get) 개체와 일치합니다.

**디바이스** 의 경우 이전 데이터 형식과 새 데이터 형식의 주목할 만한 차이점은 다음과 같습니다.
- 디바이스의 `@id`가 제거되고, `deviceId`가 `id`로 이름이 바뀌었습니다. 
- 디바이스의 프로비저닝 상태를 설명하기 위해 `provisioned` 플래그가 추가되었습니다.
- 디바이스의 승인 상태를 설명하기 위해 `approved` 플래그가 추가되었습니다.
- 디바이스 및 클라우드 속성을 포함하는 `properties`가 퍼블릭 API의 엔터티와 일치합니다.

**디바이스 템플릿** 의 경우 이전 데이터 형식과 새 데이터 형식의 주목할 만한 차이점은 다음과 같습니다.

- 디바이스 템플릿의 `@id`가 `id`로 이름이 바뀌었습니다.
- 디바이스 템플릿의 `@type`이 `types`로 이름이 바뀌었고 이제 배열입니다.

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>디바이스(2020년 2월 3일부터 사용되지 않는 형식)

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>디바이스 템플릿(2020년 2월 3일부터 사용되지 않는 형식)

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

Azure Event Hubs, Azure Service Bus 및 Azure Blob Storage에 데이터를 내보내는 방법을 알아보았으므로 이제 다음 단계를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Databricks를 사용하여 사용자 지정 분석을 실행하는 방법](./howto-create-custom-analytics.md)
