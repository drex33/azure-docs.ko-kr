---
title: REST API를 사용하여 Azure IoT Central에서 작업 관리
description: IoT Central REST API를 사용하여 애플리케이션에서 작업을 만들고 관리하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 06/21/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9eeb3619697e1b602053cac77fc42ae13663c388
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113516194"
---
# <a name="how-to-use-the-iot-central-rest-api-to-create-and-manage-jobs"></a>IoT Central REST API를 사용하여 작업을 만들고 관리하는 방법

IoT Central REST API를 사용하면 IoT Central 애플리케이션과 통합되는 클라이언트 애플리케이션을 개발할 수 있습니다. REST API를 사용하여 IoT Central 애플리케이션에서 작업을 만들고 관리할 수 있습니다. REST API를 사용하면 다음을 수행할 수 있습니다.

- 응용 프로그램에서 작업을 나열하고 작업 세부 정보를 봅니다.
- 애플리케이션에서 작업 만들기 및 실행
- 애플리케이션에서 작업을 중지, 다시 시작 및 다시 실행합니다.

> [!IMPORTANT]
> API는 현재 미리 보기로 제공됩니다. 이 문서에 설명된 모든 REST API 호출에는 `?api-version=preview`이 포함되어야 합니다.

이 문서에서는 `/jobs/{job_id}` API를 사용하여 기기를 일괄 제어하는 방법을 설명합니다. 디바이스를 개별적으로 제어할 수도 있습니다.

모든 IoT Central REST API 호출에는 권한 부여 헤더가 필요합니다. 자세히 알아보려면 [IoT Central REST API 호출을 인증하고 권한을 부여하는 방법](howto-authorize-rest-api.md)을 참조하세요.

IoT Central REST API에 대한 참조 설명서는 [Azure IoT Central REST API 참조](/rest/api/iotcentral/)를 참조하세요.

## <a name="job-payloads"></a>작업 페이로드

이 문서에서 설명하는 대부분의 API에는 다음 JSON 조각과 같은 정의가 포함되어 있습니다.

```json
{
  "id": "job-014",
  "displayName": "Set target temperature",
  "description": "Set target temperature for all thermostat devices",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "56"
    }
  ],
  "status": "complete"
}
```

다음 표에서는 이전 JSON 조각의 필드에 대해 설명합니다.

| 필드 | 설명 |
| ----- | ----------- |
| `id` | 애플리케이션의 작업에 대한 고유 ID입니다. |
| `displayName` | 애플리케이션에서 작업의 표시 이름입니다. |
| `description` | 작업에 대한 설명입니다. |
| `group` | 작업이 적용되는 디바이스 그룹의 ID입니다. `deviceGroups` 미리보기 REST API를 사용하여 애플리케이션의 기기 그룹 목록을 가져옵니다. |
| `status` | 작업의 [상태](howto-manage-devices-in-bulk.md#view-job-status). `complete`, `cancelled`, `failed`, `pending`, `running`, `stopped` 중 하나입니다. |
| `batch` | 있는 경우 이 섹션은 작업에서 기기를 [일괄 처리](howto-manage-devices-in-bulk.md#create-and-run-a-job)하는 방법을 정의합니다. |
| `batch/type` | 각 배치의 크기는 그룹에 있는 총 기기의 `percentage` 또는 기기의 `number`입니다. |
| `batch/value` | 장치의 백분율 또는 각 배치의 장치 수입니다. |
| `cancellationThreshold` | 있는 경우 이 섹션은 작업에 대한 [취소 임계값](howto-manage-devices-in-bulk.md#create-and-run-a-job)을 정의합니다. |
| `cancellationThreshold/batch` | `true` 또는 `false`. true인 경우 각 배치에 대해 취소 임계값이 설정됩니다. `false`인 경우 취소 임계값이 전체 작업에 적용됩니다. |
| `cancellationThreshold/type` | 작업의 취소 임계값은 기기의 `percentage` 또는 `number`입니다. |
| `cancellationThreshold/value` | 취소 임계값을 정의하는 장치의 백분율 또는 장치 수입니다. |
| `data` | 작업이 수행하는 작업의 배열입니다. |
| `data/type` | `PropertyJobData`, `CommandJobData` 또는 `CloudPropertyJobData` 중 하나입니다. |
| `data/target` | 대상 장치의 모델 ID입니다. |
| `data/path` | 속성, 명령 또는 클라우드 속성의 이름입니다. |
| `data/value` | 설정할 속성 값 또는 보낼 명령 매개 변수입니다. |

## <a name="get-job-information"></a>작업 정보 얻기

다음 요청을 사용하여 애플리케이션의 작업 목록을 검색합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs?api-version=preview
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "value": [
    {
      "id": "job-006",
      "displayName": "Set customer name",
      "description": "Set the customer name cloud property",
      "group": "4fcbec3b-5ee8-4324-855d-0f03b56bcf7f",
      "data": [
        {
          "type": "CloudPropertyJobData",
          "target": "dtmi:modelDefinition:bojo9tfju:yfvu5gv2vl",
          "path": "CustomerName",
          "value": "Contoso"
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-005",
      "displayName": "Set target temperature",
      "description": "Set target temperature device property",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "data": [
        {
          "type": "PropertyJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "targetTemperature",
          "value": 56
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-004",
      "displayName": "Run device report",
      "description": "Call command to run the device reports",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "batch": {
        "type": "percentage",
        "value": 25
      },
      "cancellationThreshold": {
        "type": "percentage",
        "value": 10,
        "batch": false
      },
      "data": [
        {
          "type": "CommandJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "getMaxMinReport",
          "value": "2021-06-15T05:00:00.000Z"
        }
      ],
      "status": "complete"
    }
  ]
}
```

다음 요청을 사용하여 ID별로 개별 작업을 검색합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004?api-version=preview
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "id": "job-004",
  "displayName": "Run device report",
  "description": "Call command to run the device reports",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "CommandJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "getMaxMinReport",
      "value": "2021-06-15T05:00:00.000Z"
    }
  ],
  "status": "complete"
}
```

다음 요청을 사용하여 작업에서 장치의 세부 정보를 검색합니다.

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004/devices?api-version=preview
```

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "value": [
    {
      "id": "therm-01",
      "status": "completed"
    },
    {
      "id": "therm-02",
      "status": "completed"
    },
    {
      "id": "therm-03",
      "status": "completed"
    },
    {
      "id": "therm-04",
      "status": "completed"
    }
  ]
}
```

## <a name="create-a-job"></a>작업 만들기

다음 요청을 사용하여 작업에서 장치의 세부 정보를 검색합니다.

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006?api-version=preview
{
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ]
}
```

이 요청에 대한 응답은 다음 예제와 같습니다. 초기 작업 상태는 `pending`입니다.

```json
{
  "id": "job-006",
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ],
  "status": "pending"
}
```

## <a name="stop-resume-and-rerun-jobs"></a>작업 중지, 다시 시작 및 다시 실행

다음 요청을 사용하여 실행 중인 작업을 중지합니다.

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/stop?api-version=preview
```

요청이 성공하면 `204 - No Content` 응답을 반환합니다.

중지된 작업을 재개하려면 다음 요청을 사용하십시오.

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/resume?api-version=preview
```

요청이 성공하면 `204 - No Content` 응답을 반환합니다.

실패한 장치에서 기존 작업을 다시 실행하려면 다음 명령을 사용하십시오.

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/rerun/rerun-001?api-version=preview
```

## <a name="next-steps"></a>다음 단계

이제 REST API를 사용하여 작업을 관리하는 방법을 배웠으므로 제안된 다음 단계는 [REST API를 사용하여 IoT Central 애플리케이션 관리](/learn/modules/manage-iot-central-apps-with-rest-api/) 방법을 배우는 것입니다.
