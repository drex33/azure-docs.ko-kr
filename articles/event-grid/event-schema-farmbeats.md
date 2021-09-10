---
title: Event Grid 원본으로서의 Azure FarmBeats
description: Azure Event Grid를 사용한 Azure FarmBeats 이벤트에 대해 제공되는 속성과 스키마를 설명합니다.
ms.topic: conceptual
ms.date: 06/06/2021
ms.openlocfilehash: 552a676fcfc457a662276aa7b5948670f700d208
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535660"
---
# <a name="azure-farmbeats-as-event-grid-source-preview"></a>Event Grid 원본으로서의 Azure FarmBeats
이 문서에서는 Azure FarmBeats 이벤트에 대한 속성과 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

|이벤트 이름 | 설명|
|-----|----|
|Microsoft.AgFoodPlatform.FarmerChanged|Farmer가 생성/업데이트/삭제되면 게시됩니다. 
|Microsoft.AgFoodPlatform.FarmChanged| 팜이 생성/업데이트/삭제되면 게시됩니다.
|Microsoft.AgFoodPlatform.BoundaryChanged|경계가 생성/업데이트/삭제되면 게시됩니다.
|Microsoft.AgFoodPlatform.FieldChanged|필드가 생성/업데이트/삭제되면 게시됩니다.
|Microsoft.AgFoodPlatform.SeasonalFieldChanged|계절 필드가 생성/업데이트/삭제되면 게시됩니다.
|Microsoft.AgFoodPlatform.SeasonChanged|계절이 생성/업데이트/삭제되면 게시됩니다.
|Microsoft.AgFoodPlatform.CropChanged|농작물이 생성/업데이트/삭제되면 게시됩니다.
|Microsoft.AgFoodPlatform.CropVarietyChanged|농작물 다양성이 생성/업데이트/삭제되면 게시됩니다.
|Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged| 위성 데이터 수집 작업의 상태가 변경(예: 작업 생성, 진행 또는 완료)되면 게시됩니다.
|Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged|날씨 데이터 수집 작업의 상태가 변경(예: 작업 생성, 진행 또는 완료)되면 게시됩니다.
|Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged| 팜 운영 데이터 수집 작업의 상태가 변경(예: 작업 생성, 진행 또는 완료)되면 게시됩니다.
|Microsoft.AgFoodPlatform.ApplicationDataChanged|애플리케이션 데이터가 생성/업데이트/삭제되면 게시됩니다. 이 이벤트는 팜 운영 데이터와 연결됩니다.
|Microsoft.AgFoodPlatform.HarvestingDataChanged|수확 데이터가 생성/업데이트/삭제되면 게시됩니다. 이 이벤트는 팜 운영 데이터와 연결됩니다.
|Microsoft.AgFoodPlatform.TillageDataChanged|경작지 데이터가 생성/업데이트/삭제되면 게시됩니다. 이 이벤트는 팜 운영 데이터와 연결됩니다.
|Microsoft.AgFoodPlatform.PlantingDataChanged|심기 데이터가 생성/업데이트/삭제되면 게시됩니다. 이 이벤트는 팜 운영 데이터와 연결됩니다.

## <a name="event-properties"></a>이벤트 속성
각 FarmBeats 이벤트에는 두 부분이 있습니다. 하나는 이벤트 전체에서 공통이고 다른 하나(데이터 개체)에는 각 이벤트에 고유한 속성이 포함되어 있습니다. 

여러 이벤트에서 공통적인 부분은 다음 스키마에 자세히 설명되어 있습니다.

### <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마
이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | App Configuration 이벤트 데이터. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |


아래 표에는 각 이벤트에 대한 데이터 개체 내의 속성이 자세히 설명되어 있습니다.

*FarmerChanged, FarmChanged, SeasonChanged, CropChanged, CropVarietyChanged FarmBeats 이벤트의 경우 데이터 개체에는 다음 속성이 포함됩니다.*

|속성 | 형식| Description|
|----| ----| ----|
id| 문자열| 팜 ID, Farmer ID 등과 같은 리소스의 사용자 정의 ID입니다.
actionType| 문자열| 이벤트를 게시하는 동안 트리거되는 변경을 나타냅니다. 적용 가능한 값은 Created, Updated, Deleted입니다.
상태| 문자열| 리소스의 사용자 정의 상태를 포함합니다.
properties| object| 사용자 정의 키/값 쌍이 포함되어 있습니다.
modifiedDateTime| 날짜-시간|리소스가 마지막으로 수정된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
createdDateTime|날짜-시간|리소스가 생성된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
eTag|   문자열| 낙관적 동시성 구현
description| 문자열|    리소스에 대한 텍스트 형식의 설명


*BoundaryChanged FarmBeats 이벤트에는 다음과 같은 데이터 개체가 있습니다.*

|속성 | 형식| Description|
|----| ----| ----|
id| 문자열| 경계의 사용자 정의 ID
actionType| 문자열| 이벤트를 게시하는 동안 트리거되는 변경을 나타냅니다. 적용 가능한 값은 Created, Updated, Deleted입니다.
parentId|   문자열| 부모 경계의 ID가 속한 경우입니다.
parentType| 문자열| 부모 경계의 유형이 속한 경우입니다.
isPrimary|  boolean|    경계가 기본 경계인지를 나타냅니다.
farmerId|   문자열| 경계와 연결된 Farmer의 ID를 포함합니다.
properties| object| 사용자 정의 키-값 쌍을 포함합니다.
modifiedDateTime| 날짜-시간|리소스가 마지막으로 수정된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
createdDateTime|날짜-시간|리소스가 생성된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
상태| 문자열| 리소스의 사용자 정의 상태를 포함합니다.
eTag|   문자열| 낙관적 동시성을 구현합니다.
description| 문자열|    리소스에 대한 텍스트 형식의 설명입니다.

*FieldChanged FarmBeats 이벤트에는 다음과 같은 데이터 개체가 있습니다.*

속성|   형식|   Description
|----| ----| ----|
id| 문자열| 필드의 사용자 정의 ID
farmId| 문자열| 필드가 연결된 팜의 사용자 정의 ID
farmerId|   문자열| 필드가 연결된 Farmer의 사용자 정의 ID
name|   문자열| 필드의 사용자 정의 이름
actionType| 문자열| 이벤트를 게시하는 동안 트리거되는 변경을 나타냅니다. 적용 가능한 값은 Created, Updated, Deleted입니다.
properties| object| 사용자 정의 키/값 쌍이 포함되어 있습니다.
modifiedDateTime| 날짜-시간|리소스가 마지막으로 수정된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
createdDateTime|날짜-시간|리소스가 생성된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
상태| 문자열| 리소스의 사용자 정의 상태를 포함합니다.
eTag|   문자열| 낙관적 동시성 구현
description|문자열| 리소스에 대한 텍스트 형식의 설명

*SeasonalFieldChanged FarmBeats 이벤트에는 다음과 같은 데이터 개체가 있습니다.*

속성|   형식|   Description
|----| ----| ----|
id| 문자열| 계절별 필드의 사용자 정의 ID
farmId| 문자열| 계절별 필드가 연결된 팜의 사용자 정의 ID
farmerId|   문자열| 계절별 필드가 연결된 Farmer의 사용자 정의 ID
seasonId|   문자열| 계절별 필드가 연결된 계절의 사용자 정의 ID
fieldId|    문자열| 계절별 필드가 연결된 필드의 사용자 정의 ID
name|   문자열| 계절별 필드의 사용자 정의 이름
actionType| 문자열| 이벤트를 게시하는 동안 트리거되는 변경을 나타냅니다. 적용 가능한 값은 Created, Updated, Deleted입니다.
properties| object| 사용자 정의 키/값 쌍이 포함되어 있습니다.
modifiedDateTime| 날짜-시간|리소스가 마지막으로 수정된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
createdDateTime|날짜-시간|리소스가 생성된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
상태| 문자열| 리소스의 사용자 정의 상태를 포함합니다.
eTag|   문자열| 낙관적 동시성 구현
description| 문자열|    리소스에 대한 텍스트 형식의 설명

*SatelliteDataIngestionJobChanged, WeatherDataIngestionJobChanged, and FarmOperationsDataIngestionJobChanged FarmBeats 이벤트에는 다음과 같은 데이터 객체가 있습니다.*

속성|   형식|   Description
|----|----|----|
id|String| 작업의 고유 ID입니다.
name| 문자열| 작업의 사용자 정의 이름입니다.
상태|문자열|작업의 있을 수 있는 다양한 상태입니다.
isCancellationRequested| boolean|작업 취소가 요청되면 설정되는 플래그입니다.
description|문자열| 작업에 대한 텍스트 설명입니다.
farmerId|문자열| 작업을 만든 Farmer의 ID입니다.
message|문자열| 작업에 대한 자세한 정보를 캡처하는 상태 메시지입니다.
lastActionDateTime|날짜-시간|작업에서 마지막 작업이 수행된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
createdDateTime|날짜-시간|리소스가 생성된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).


*ApplicationDataChanged, HarvestingDataChanged, PlantingDataChanged 및 TillageDataChanged 등의 FarmBeats 팜 운영 데이터 변경 이벤트에는 다음과 같은 데이터 개체가 있습니다.*

속성|   형식|   Description
|----|----|----|
id| 문자열| 팜 ID, Farmer ID 등과 같은 리소스의 사용자 정의 ID입니다.
상태| 문자열| 작업의 상태가 포함되어 있습니다. 
actionType|문자열|
source| 문자열| 작업에 대한 세부 정보를 제공하는 FarmBeats의 메시지입니다.    
modifiedDateTime| 날짜-시간|리소스가 마지막으로 수정된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
createdDateTime|날짜-시간|리소스가 생성된 날짜-시간입니다(샘플 형식: yyyy-MM-ddTHH:mm:ssZ).
eTag|   문자열| 낙관적 동시성 구현
description|문자열| 리소스에 대한 텍스트 형식의 설명


## <a name="sample-events"></a>샘플 이벤트 
이러한 이벤트 샘플은 이벤트 알림을 나타냅니다.

**이벤트 유형: Microsoft.AgFoodPlatform.FarmerChanged**

```json
{
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:53:28Z",
      "eTag": "860197cc-0000-0700-0000-60420da80000",
      "id": "UNIQUE-FARMER-ID",
      "name": "sample farmer",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:53:28Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "81fbe1de-4ae4-4284-964f-59da80a6bfe7",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmerChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:53:28.2783745Z"
  }
```

**이벤트 유형: Microsoft.AgFoodPlatform.FarmChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:55:57Z",
      "eTag": "8601e3d5-0000-0700-0000-60420e3d0000",
      "id": "UNIQUE-FARM-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:55:57Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "31a31be7-51fb-48f3-adfd-6fb4400be002",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farms/UNIQUE-FARM-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:55:57.6026173Z"
  }
```
**이벤트 유형: Microsoft.AgFoodPlatform.BoundaryChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "parentId": "OPTIONAL-UNIQUE-FIELD-ID",
      "isPrimary": true,
      "actionType": "Created",
      "modifiedDateTime": "2021-03-05T11:15:29Z",
      "eTag": "860109f7-0000-0700-0000-604212d10000",
      "id": "UNIQUE-BOUNDARY-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:15:29Z"
    },
    "id": "3d3453b2-5a94-45a7-98eb-fc2979a00317",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/boundaries/UNIQUE-BOUNDARY-ID",
    "eventType": "Microsoft.AgFoodPlatform.BoundaryChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:15:29.4797354Z"
  }
  ```

**이벤트 유형: Microsoft.AgFoodPlatform.FieldChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:58:43Z",
      "eTag": "860124dc-0000-0700-0000-60420ee30000",
      "id": "UNIQUE-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:58:43Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "1ad04ed0-ac05-4c4e-aa3d-87facb3cc97c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/fields/UNIQUE-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.FieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:58:43.3222921Z"
  }
  ```
**이벤트 유형: Microsoft.AgFoodPlatform.SeasonalFieldChanged**
```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "seasonId": "UNIQUE-SEASON-ID",
      "fieldId": "UNIQUE-FIELD-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:24:56Z",
      "eTag": "8701300b-0000-0700-0000-604215080000",
      "id": "UNIQUE-SEASONAL-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:24:56Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ff59a0a3-6226-42c0-9e70-01da55efa797",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/seasonalFields/UNIQUE-SEASONAL-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonalFieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:24:56.4210287Z"
  }
```
**이벤트 유형: Microsoft.AgFoodPlatform.SeasonChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:18:38Z",
      "eTag": "86019afd-0000-0700-0000-6042138e0000",
      "id": "UNIQUE-SEASON-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:18:38Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "63989475-397b-4b92-8160-8743bf8e5804",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/seasons/UNIQUE-SEASON-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:18:38.5804699Z"
  }
  ```

  **이벤트 유형: Microsoft.AgFoodPlatform.CropChanged**

```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:03:48Z",
      "eTag": "8601c4e5-0000-0700-0000-604210150000",
      "id": "UNIQUE-CROP-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:03:48Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "4c59a797-b76d-48ec-8915-ceff58628f35",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/crops/UNIQUE-CROP-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:03:49.0590658Z"
  }
  ```

**이벤트 유형: Microsoft.AgFoodPlatform.CropVarietyChanged**

```json
  {
    "data": {
      "cropId": "UNIQUE-CROP-ID",
      "actionType": "Created",
      "status": "string",
      "modifiedDateTime": "2021-03-05T11:10:21Z",
      "eTag": "860130ef-0000-0700-0000-6042119d0000",
      "id": "UNIQUE-CROP-VARIETY-ID",
      "name": "Sample status",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:10:21Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "29aefdb9-d648-442c-81f8-694f3f47583c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/cropVarieties/UNIQUE-CROP-VARIETY-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropVarietyChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:10:21.4572495Z"
  }
```
**이벤트 유형: Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE - FARMER - ID",
      "message": "Created job 'job1' to fetch satellite data for boundary 'boundary1' from startDate '06/01/2021' to endDate '06/01/2021' (both inclusive).",
      "status": "Waiting",
      "lastActionDateTime": "2021-06-01T11:25:37.8634096Z",
      "isCancellationRequested": false,
      "id": "UNIQUE - JOB - ID",
      "name": "samplejob",
      "description": "Sample for testing events",
      "createdDateTime": "2021-06-01T11:25:32.3421173Z",
      "properties": {
        "key1": "testvalue1",
        "key2": 123.45
      }
    },
    "id": "925c6be2-6561-4572-b7dd-0f3084a54567",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/{UNIQUE-FARMER-ID}/satelliteDataIngestionJobs/{UNIQUE-JOB-ID}",
    "eventType": "Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:25:37.8634764Z"
  }
]
```
**이벤트 유형: Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Created job to fetch weather data for job name 'job2', farmer id 'farmer2' and boundary id 'boundary2'.",
      "status": "Running",
      "lastActionDateTime": "2021-06-01T11:22:27.9031003Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "createdDateTime": "2021-06-01T07:13:54.8843617Z"
    },
    "id": "ec30313a-ff2f-4b50-882b-31188113c15b",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/weatherDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:22:27.9031302Z"
  }
]

```
**이벤트 유형: Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Job completed successfully. Data statistics:{ Processed operations count = 6, Organizations count = 1, Processed organizations count = 1, Processed fields count = 2, Operations count = 6, ShapefileAttachmentsCount = 0, Fields count = 2 }",
      "status": "Succeeded",
      "lastActionDateTime": "2021-06-01T11:30:54.733625Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "name": "sample-job",
      "description": "sample description",
      "createdDateTime": "2021-06-01T11:30:39.0905288Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ebdbb7a1-ad28-4af7-b3a2-a4a3a2dd1b4f",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farmOperationDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:30:54.733671Z"
  }
]

```
**이벤트 유형: Microsoft.AgFoodPlatform.ApplicationDataChanged**

```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:27:24Z",
      "eTag": "87011311-0000-0700-0000-6042159c0000",
      "id": "UNIQUE-APPLICATION-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:27:24Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "e499f6c4-63ba-4217-8261-0c6cb0e398d2",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/applicationData/UNIQUE-APPLICATION-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.ApplicationDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:27:24.164612Z"
  }
```

**이벤트 유형: Microsoft.AgFoodPlatform.HarvestDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:33:41Z",
      "eTag": "8701141b-0000-0700-0000-604217150000",
      "id": "UNIQUE-HARVEST-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:33:41Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "dc3837c0-1eed-4bfa-88b6-d018cf6af4db",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/harvestData/UNIQUE-HARVEST-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.HarvestDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:33:41.3434992Z"
  }
```
**이벤트 유형: Microsoft.AgFoodPlatform.TillageDataChanged**
```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "sample source",
      "modifiedDateTime": "2021-06-15T10:31:07Z",
      "eTag": "6405f027-0000-0100-0000-60c8816b0000",
      "id": "c9858c3f-fb94-474a-a6de-103b453df976",
      "createdDateTime": "2021-06-15T10:31:07Z",
      "name": "sample name",
      "description":"sample description"
      "properties": {
        "_orgId": "498221",
        "_fieldId": "e61b83f4-3a12-431e-8010-596f2466dc27",
        "_cropSeason": "2010"
      }
    },
    "id": "f06f6686-1fa8-41fd-be99-46f40f495cce",
    "topic": "/subscriptions/da9091ec-d18f-456c-9c21-5783ee7f4645/resourceGroups/internal-farmbeats-resources/providers/Microsoft.AgFoodPlatform/farmBeats/internal-eus",
    "subject": "/farmers/10e3d7bf-c559-48be-af31-4e00df83bfcd/tillageData/c9858c3f-fb94-474a-a6de-103b453df976",
    "eventType": "Microsoft.AgFoodPlatform.TillageDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-15T10:31:07.6778047Z"
  }
```

**이벤트 유형: Microsoft.AgFoodPlatform.PlantingDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:41:18Z",
      "eTag": "8701242a-0000-0700-0000-604218de0000",
      "id": "UNIQUE-PLANTING-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:41:18Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "42589c7f-4e16-4a4d-9314-d611c822f7ac",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/plantingData/UNIQUE-PLANTING-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.PlantingDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:41:18.1744322Z"
  }
```



## <a name="next-steps"></a>다음 단계
* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
