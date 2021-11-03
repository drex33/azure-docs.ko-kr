---
title: REST API를 사용 하 여 Azure IoT Central에서 데이터 내보내기 관리
description: IoT Central REST API를 사용 하 여 응용 프로그램에서 데이터 내보내기를 관리 하는 방법
author: v-krishnag
ms.author: v-krishnag
ms.date: 10/18/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cb538b6c8777a9aeb1d02fe705a87831f7aaf5b9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433784"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-data-exports"></a>IoT Central REST API를 사용 하 여 데이터 내보내기를 관리 하는 방법

IoT Central REST API를 사용하면 IoT Central 애플리케이션과 통합되는 클라이언트 애플리케이션을 개발할 수 있습니다. REST API를 사용 하 여 IoT Central 응용 프로그램에서 [데이터 내보내기를](howto-export-data.md) 만들고 관리할 수 있습니다.

모든 IoT Central REST API 호출에는 권한 부여 헤더가 필요합니다. 자세히 알아보려면 [IoT Central REST API 호출을 인증하고 권한을 부여하는 방법](howto-authorize-rest-api.md)을 참조하세요.

IoT Central REST API에 대한 참조 설명서는 [Azure IoT Central REST API 참조](/rest/api/iotcentral/)를 참조하세요.

## <a name="data-export"></a>데이터 내보내기

IoT Central 데이터 내보내기 기능을 사용 하 여 원격 분석, 속성 변경, 장치 연결 이벤트, 장치 수명 주기 이벤트, 장치 템플릿 수명 주기 이벤트를 azure Event Hubs, azure Service Bus, Azure Blob Storage 및 webhook 끝점과 같은 대상으로 스트리밍할 수 있습니다.

각 데이터 내보내기 정의는 하나 이상의 대상으로 데이터를 보낼 수 있습니다. 내보내기 정의를 만들기 전에 대상 정의를 만듭니다.

### <a name="create-or-update-a-destination"></a>대상 만들기 또는 업데이트

대상 정의를 만들거나 업데이트 하려면 다음 요청을 사용 합니다.

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

* destinationId-대상의 고유 ID입니다.

다음 예제에서는 blob 저장소 대상을 만드는 요청 본문을 보여 줍니다.

```json
{
  "displayName": "Blob Storage Destination",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

요청 본문에는 다음과 같은 몇 가지 필수 필드가 있습니다.

* `displayName`: 대상의 표시 이름입니다.
* `type`: `blobstorage@v1` ,, `dataexplorer@v1` ,, `eventhubs@v1` `servicebusqueue@v1` `servicebustopic@v1` , `webhook@v1` 중 하나일 수 있는 대상 개체의 형식입니다.
* `connectionString`: 대상 리소스에 액세스 하기 위한 연결 문자열입니다.
* `containerName`: Blob 저장소 대상의 경우 데이터를 써야 하는 컨테이너의 이름입니다.

이 요청에 대한 응답은 다음 예제와 같습니다. 

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="get-a-destination-by-id"></a>ID로 대상 가져오기

응용 프로그램에서 대상의 세부 정보를 검색 하려면 다음 요청을 사용 합니다.

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-destinations"></a>대상 나열

응용 프로그램에서 대상 목록을 검색 하려면 다음 요청을 사용 합니다.

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations?api-version=1.1-preview
```

이 요청에 대한 응답은 다음 예제와 같습니다. 

```json
{
    "value": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
            "displayName": "Blob Storage Destination",
            "type": "blobstorage@v1",
            "authorization": {
                "type": "connectionString",
                "connectionString": DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
                "containerName": "central-data"
            },
            "status": "waiting"
        },
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9",
            "displayName": "Webhook destination",
            "type": "webhook@v1",
            "url": "http://requestbin.net/r/f7x2i1ug",
            "headerCustomizations": {},
            "status": "error",
        }
        }
    ]
}
```

### <a name="patch-a-destination"></a>대상 패치

```http
PATCH https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

내보내기에 대 한 증분 업데이트를 수행 하는 데 사용할 수 있습니다. 샘플 요청 본문은를 대상으로 업데이트 하는 다음 예제와 같습니다 `displayName` .

```json
{
  "displayName": "Blob Storage",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}   
```

### <a name="delete-a-destination"></a>대상 삭제

대상을 삭제 하려면 다음 요청을 사용 합니다.

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

### <a name="create-or-update-an-export-definition"></a>내보내기 정의 만들기 또는 업데이트

데이터 내보내기 정의를 만들거나 업데이트 하려면 다음 요청을 사용 합니다.

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

다음 예제에서는 장치 원격 분석에 대 한 내보내기 정의를 만드는 요청 본문을 보여 줍니다.

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150"
        }
    ]
}
```

요청 본문에는 다음과 같은 몇 가지 필수 필드가 있습니다.

* `displayName`: 내보내기의 표시 이름입니다.
* `enabled`: 내보내기 시작/중지를 전환 하 여 데이터를 보냅니다.
* `source`: 내보낼 데이터 형식입니다.
* `destinations`: 내보내기가 데이터를 전송 해야 하는 대상의 목록입니다. 대상 Id가 응용 프로그램에 이미 있어야 합니다.

내보내기에 세부 정보를 추가 하는 데 사용할 수 있는 몇 가지 선택적 필드가 있습니다.

* `enrichments`: 전송 된 각 메시지에 포함할 추가 정보 부분입니다. 데이터는 키/값 쌍 집합으로 표시 됩니다. 여기서 키는 출력 메시지에 표시 되는 보강의 이름이 고 값은 보낼 데이터를 식별 합니다.
* `filter`: 원본에서 내보낼 이벤트를 정의 하는 쿼리입니다.

이 요청에 대한 응답은 다음 예제와 같습니다. 

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "starting"
}
```

### <a name="get-an-export-by-id"></a>ID로 내보내기 가져오기

응용 프로그램에서 내보내기 정의의 세부 정보를 검색 하려면 다음 요청을 사용 합니다.

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-export-definitions"></a>내보내기 정의 나열

응용 프로그램에서 내보내기 정의 목록을 검색 하려면 다음 요청을 사용 합니다.

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports?api-version=1.1-preview
```

이 요청에 대한 응답은 다음 예제와 같습니다. 

```json
{
  "value": [
    {
      "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "starting"
    },
    {
      "id": "802894c4-33bc-4f1e-ad64-e886f315cece",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "healthy"
    }
  ]
}
```

### <a name="patch-an-export-definition"></a>내보내기 정의 패치

```http
PATCH https://{subdomain}.{baseDomain}/dataExport/exports/{exportId}?api-version=1.1-preview
```

내보내기에 대 한 증분 업데이트를 수행 하는 데 사용할 수 있습니다. 예제 요청 본문은를 내보내기로 업데이트 하는 다음 예제와 같습니다 `enrichments` .

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value 2"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ]
}
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "healthy"
}
```

### <a name="delete-an-export-definition"></a>내보내기 정의 삭제

내보내기 정의를 삭제 하려면 다음 요청을 사용 합니다.

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

## <a name="next-steps"></a>다음 단계

이제 REST API를 사용 하 여 데이터 내보내기를 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Central REST API를 사용 하 여 장치 템플릿을 관리 하는 방법을 설명](howto-manage-device-templates-with-rest-api.md)하는 것입니다.
