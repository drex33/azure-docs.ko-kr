---
title: 작업 라우터의 이벤트 구독
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services Sdk를 사용 하 여 Event Grid에서 작업 라우터 이벤트 구독
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f496ed46c8982749104ccc96683a54cae88e861
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132866238"
---
# <a name="subscribe-to-job-router-events"></a>작업 라우터 이벤트 구독

이 가이드에서는 Azure Communication Services Event Grid 구독에서 작업 라우터 이벤트를 구독 하는 단계를 간략하게 설명 합니다. 이벤트 수신은 사용자 지정 응용 프로그램에서 수행 해야 하는 중요 한 기능입니다. 작업 라우터는 전송 하는 작업에 대해 비동기적으로 수행 되며, SDK는 시스템의 개체 상태와 상태를 쿼리 하는 끝점을 제공 하는 반면, 반응 이벤트 기반 사용자 지정 응용 프로그램을 빌드하면 상당한 이점이 있습니다.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 선택 사항: [작업 라우터를 시작](../../quickstarts/router/get-started-router.md) 하려면 빠른 시작을 완료 하세요.
- [ARM (Azure Resource Manager) 클라이언트](https://github.com/projectkudu/ARMClient) 설치
- [Event Grid Viewer를 사용 하 여 GitHub 샘플 프로젝트](https://github.com/Azure-Samples/azure-event-grid-viewer) 를 검토 합니다.

## <a name="create-an-event-grid-subscription"></a>Event Grid 구독 만들기

> [!NOTE]
> PowerShell을 사용 하 여 다음 스크립트를 실행 합니다.

**Azure 계정에 로그인**

```powershell
armclient azlogin
```

**구독 및 리소스 그룹 이름 설정**
```powershell
$env:SUB = 'subscriptions/<insert_subscription_id>'
$env:RG = 'resourcegroups/<insert_resource_group_name>'
```

**필드 구독의 모든 ACS 리소스 나열**
```powershell
armclient get "/$env:SUB/$env:RG/providers/Microsoft.Communication/communicationservices?api-version=2020-08-20"
```
**출력**

:::image type="content" source="media/create-subscription-output.png" alt-text="PowerShell 명령의 출력":::

여기에서 볼 수 있듯이, 현재는 지정 된 구독 및 리소스 그룹에 Azure Communication Services 리소스가 하나만 있습니다.

**ACS 리소스 이름 설정**

Azure Communication Services 리소스의 이름을 설정 합니다. 예를 들어 리소스에 대 한 끝점이 인 경우를 `https://contoso.communication.azure.net` `ACS_RESOURCE_NAME` DNS 이름의 접두사로 설정 `contoso` 합니다.

```powershell
$env:ACS_RESOURCE_NAME = "<insert_acs_resource_name>"
```

**필드 기존 이벤트 구독 나열**

기존 이벤트 구독이 있는지 확인 합니다.

```powershell
$env:ACS_RESOURCE_ARM_ID = "/$env:SUB/$env:RG/providers/Microsoft.Communication/CommunicationServices/$env:ACS_RESOURCE_NAME"
$env:API_VERSION = "?api-version=2020-06-01"
$env:EVENT_SUBSCRIPTIONS_PATH = "providers/Microsoft.EventGrid/eventSubscriptions"

armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```
:::image type="content" source="media/check-subscriptions.png" alt-text="기존 구독 확인":::

여기에서 볼 수 있듯이이 리소스에 대 한 이벤트 구독은 생성 되지 않습니다.

**라우터 이벤트에 대 한 새 이벤트 구독 만들기**

다음 json 페이로드를 복사 하 여 라는 텍스트 파일에 붙여 넣습니다 `test.json` .

*샘플 페이로드*
```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>"
      }
    },
    "filter": {
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobReceived",
        "Microsoft.Communication.RouterJobClassified",
        "Microsoft.Communication.RouterJobLabelsUpdated",
        "Microsoft.Communication.RouterJobClassificationFailed",
        "Microsoft.Communication.RouterJobCompleted",
        "Microsoft.Communication.RouterJobClosed",
        "Microsoft.Communication.RouterJobCancelled",
        "Microsoft.Communication.RouterJobExceptionTriggered",
        "Microsoft.Communication.RouterJobExceptionCleared",
        "Microsoft.Communication.RouterWorkerOfferIssued",
        "Microsoft.Communication.RouterWorkerOfferAccepted",
        "Microsoft.Communication.RouterWorkerOfferDeclined",
        "Microsoft.Communication.RouterWorkerOfferRevoked",
        "Microsoft.Communication.RouterWorkerOfferExpired",
        "Microsoft.Communication.RouterWorkerRegistered",
        "Microsoft.Communication.RouterWorkerDeregistered"
      ],
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    }
  }
}
```

**이벤트 구독 이름 설정**
```powershell
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_All"
```

**이벤트 구독 만들기**
```powershell
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test.json
```
**출력**

:::image type="content" source="media/create-subscription.png" alt-text="이벤트 구독 만들기":::

여기에서 볼 수 있듯이 이벤트 구독은 만들어지고 현재 상태입니다 `Creating` . 일반적으로 만드는 데 몇 초 정도 걸립니다.

**이벤트 구독이 성공적으로 만들어졌는지 확인 합니다.**
```powershell
armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```

**출력**

:::image type="content" source="media/verify-subscription-created.png" alt-text="구독이 생성 되었는지 확인":::

이제 모든 라우터 이벤트에 대해 이벤트 구독을 성공적으로 만들었습니다.

## <a name="creating-a-subscription-with-filters"></a>필터를 사용 하 여 구독 만들기

이벤트 구독을 설정 하는 동안 특정 구독에 전송 해야 하는 정확한 이벤트를 제어 하는 고급 필터를 사용할 수도 있습니다. 예를 들어 아래 샘플에서는 `RouterJobCancelled` 다음 조건에 따라 이벤트만 구독 하 고 webhook에 전송 됩니다.

- 작업 **우선 순위가** 다음 보다 큽니다. `5`
- 작업이 에스컬레이션 큐에 할당 되었습니다.
- 작업이 비활성 상태로 인해 취소 되었습니다.
- 취소 된 작업에 대 한 처리 코드는 다음으로 끝납니다. `_JobCancelledDueToInactivity`
- 큐 ID는 이름으로 끝납니다. `EscalationQueue`

```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>",
        "maxEventsPerBatch": 1,
        "preferredBatchSizeInKilobytes": 64
      }
    },
    "filter": {
      "subjectEndsWith": "_JobCancelledDueToInactivity",
      "isSubjectCaseSensitive": true,
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobCancelled"
      ],
      "advancedFilters": [
        {
          "operatorType": "NumberGreaterThan",
          "key": "data.priority",
          "value": 5
        },
        {
          "operatorType": "StringEndsWith",
          "key": "data.queueId",
          "values": [
            "EscalationQueue"
          ]
        }
      ],
      "enableAdvancedFilteringOnArrays": true
    }
  }
}
```

위의 json 페이로드를 복사 하 여 텍스트에 붙여넣은 `test-with-advanced-filters.json` 후 다음 PowerShell 코드를 실행 합니다.

```powershell
$env:API_VERSION = "?api-version=2020-10-15-preview"
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_WithFilters"
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test-with-advanced-filters.json
```

**출력**

:::image type="content" source="media/advanced-filters.png" alt-text="고급 필터 출력":::

> [!NOTE]
> 구독을 만드는 동안 사용할 수 있는 연산자의 전체 목록은 Event Grid |을 참조 하세요. [ 이벤트 필터링-연산자](../../../event-grid/event-filtering.md)

## <a name="events-catalog"></a>이벤트 카탈로그

| 이벤트 | 하위 도메인 | 설명 |
|------|:------------:| ---------- |
| [`RouterJobReceived`](#microsoftcommunicationrouterjobreceived) | `Job` | 라우팅을 위해 새 작업을 만들었습니다. |
| [`RouterJobClassified`](#microsoftcommunicationrouterjobclassified)| `Job` | 분류 정책이 작업에 적용 되었습니다. |
| [`RouterJobLabelsUpdated`](#microsoftcommunicationrouterjoblabelsupdated) | `Job` | 작업의 레이블이 변경 되었습니다. |
| [`RouterJobClassificationFailed`](#microsoftcommunicationrouterjobclassificationfailed) | `Job` | 라우터가 분류 정책을 사용 하 여 작업을 분류 하지 못함 |
| [`RouterJobCompleted`](#microsoftcommunicationrouterjobcompleted) | `Job` | 작업이 완료 되 고 줄 바꿈 입력 |
| [`RouterJobClosed`](#microsoftcommunicationrouterjobclosed) | `Job` | 작업이 닫히고 래핑 완료 됨 |
| [`RouterJobCancelled`](#microsoftcommunicationrouterjobcancelled) | `Job` | 작업이 취소 되었습니다. |
| [`RouterJobExceptionTriggered`](#microsoftcommunicationrouterjobexceptiontriggered) | `Job` | 작업 예외가 트리거 되었습니다. |
| [`RouterJobExceptionCleared`](#microsoftcommunicationrouterjobexceptioncleared) | `Job` | 작업 예외가 지워졌습니다. |
| [`RouterWorkerOfferIssued`](#microsoftcommunicationrouterworkerofferissued) | `Worker` | 작업을 작업자에 게 제공 했습니다. |
| [`RouterWorkerOfferAccepted`](#microsoftcommunicationrouterworkerofferaccepted) | `Worker` | 작업자에 대 한 제안이 수락 되었습니다. |
| [`RouterWorkerOfferDeclined`](#microsoftcommunicationrouterworkerofferdeclined) | `Worker` | 작업자에 대 한 제안이 거부 되었습니다. |
| [`RouterWorkerOfferRevoked`](#microsoftcommunicationrouterworkerofferrevoked)  | `Worker` | 작업자에 대 한 제안이 해지 되었습니다. |
| [`RouterWorkerOfferExpired`](#microsoftcommunicationrouterworkerofferexpired)  | `Worker` | 작업자에 대 한 제안이 만료 되었습니다. |
| [`RouterWorkerRegistered`](#microsoftcommunicationrouterworkerregistered)  | `Worker` | 작업자를 등록 했습니다. |
| [`RouterWorkerDeregistered`](#microsoftcommunicationrouterworkerderegistered)  | `Worker` | 작업자의 등록이 취소 되었습니다. |

## <a name="router-events"></a>라우터 이벤트

### <a name="microsoftcommunicationrouterjobreceived"></a>Microsoft Communication Jobreceived

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "acdf8fa5-8ab4-4a65-874a-c1d2a4a97f2e",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "PendingClassification",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "queue-id",
    "priority": 0,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | 가능한 값 <ul> <li>PendingClassification</li> </ul> | 이 이벤트가 전송 되 면 분류 프로세스가 아직 실행 되지 않은 것입니다. |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null``queueId`작업에 대해가 지정 된 경우 |
| queueId | `string` | ✔️ | | `null``classificationPolicyId`작업에 대해가 지정 된 경우 |
| priority | `int` | ✔️ | | `classificationPolicyId`가 지정 된 경우 Null입니다. 직접 큐 할당이 있는 경우 null이 아닌 값입니다. |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |

### <a name="microsoftcommunicationrouterjobclassified"></a>Microsoft..

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/queue/{queue-id}",
  "data": {
    "queueInfo": {
      "id": "625fec06-ab81-4e60-b780-f364ed96ade1",
      "name": "Queue 1",
      "labels": {
        "Language": "en",
        "Product": "Office",
        "Geo": "NA"
      }
    },
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassified",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| queueInfo | `QueueInfo` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null``queueId`작업에 대해가 지정 된 경우 (직접 큐 할당) |
| queueId | `string` | ✔️ | | `null``classificationPolicyId`작업에 대해가 지정 된 경우 |
| priority | `int` | ❌ |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |

### <a name="microsoftcommunicationrouterjoblabelsupdated"></a>Microsoft. Routerjoblabel의 최신 정보

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "Queued",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labelsAddedOrChanged": {
      "English": "5",
      "Office": "7"
    },
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken",
      "English": "5",
      "Office": "7"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobLabelsUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | 가능한 값 <ul> <li>PendingClassification</li> <li>Queued</li> <li>할당됨</li> <li>Completed</li> <li>폐쇄형</li> <li>취소됨</li> <li>ClassificationFailed</li> </ul> |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null``queueId`작업에 대해가 지정 된 경우 |
| queueId | `string` | ✔️ | | `null``classificationPolicyId`작업에 대해가 지정 된 경우 |
| priority | `int` | ❌ |
| labelsAddedOrChanged | `Dictionary<string, object>` | ✔️ | | 사용자 입력에 따라 레이블을 추가 하거나 변경 합니다. |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 작업에 연결 된 레이블의 전체 집합입니다. |

### <a name="microsoftcommunicationrouterjobclassificationfailed"></a>RouterJobClassificationFailed

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/classificationpolicy/{classificationpolicy-id}",
  "data": {
    "errors": [
      {
        "code": null,
        "message": "Classification failed due to <reason>",
        "target": null,
        "innerError": null,
        "details": null
      }
    ],
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassificationFailed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| 오류| `List<CommunicationError>` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ❌ | |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |

### <a name="microsoftcommunicationrouterjobcompleted"></a>Microsoft. RouterJobCompleted

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "queue-id",
    "assignmentId": "6f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
    "workerId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCompleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| assignmentId| `string` | ❌ | |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |
| workerId | `string` | ❌ | |

### <a name="microsoftcommunicationrouterjobclosed"></a>Microsoft.-통신.

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "",
    "dispositionCode": "",
    "workerId": "",
    "assignmentId": "",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClosed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| dispositionCode| `string` | ✔️ | | 사용자 입력 기반 |
| workerId | `string` | ❌ | |
| assignmentId | `string` | ❌ | |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |

### <a name="microsoftcommunicationrouterjobcancelled"></a>Microsoft. RouterJobCancelled 됨

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/disposition/{disposition-code}",
  "data": {
    "note": "Cancelled due to <reason>",
    "dispositionCode": "100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "queueId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCancelled",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| 참고| `string` | ✔️ | | 사용자 입력 기반 |
| dispositionCode| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ✔️ | | 분류가 완료 된 후 작업이 취소 되 면 null이 아닙니다. |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |

### <a name="microsoftcommunicationrouterjobexceptiontriggered"></a>Microsoft. RouterJobExceptionTriggered

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "exceptionRuleId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionTriggered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| exceptionRuleId| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |

### <a name="microsoftcommunicationrouterjobexceptioncleared"></a>Microsoft. RouterJobExceptionCleared

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionCleared",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| 레이블 | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |

## <a name="worker-events"></a>작업자 이벤트

### <a name="microsoftcommunicationrouterworkerofferissued"></a>RouterWorkerOfferIssued

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "525fec06-ab81-4e60-b780-f364ed96ade1",
    "offerTimeUtc": "2021-06-23T02:43:30.3847144Z",
    "expiryTimeUtc": "2021-06-23T02:44:30.3847674Z",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferIssued",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId| `string` | ❌ |
| offerTimeUtc | `DateTimeOffset` | ❌ |
| expiryTimeUtc| `DateTimeOffset` | ❌ |
| jobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |

### <a name="microsoftcommunicationrouterworkerofferaccepted"></a>RouterWorkerOfferAccepted

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "assignmentId": "765fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferAccepted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| jobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |
| assignmentId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferdeclined"></a>Microsoft.Communication.RouterWorkerOfferDeclined

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferDeclined",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferrevoked"></a>Microsoft.Communication.RouterWorkerOfferRevoked

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferRevoked",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferexpired"></a>Microsoft.Communication.RouterWorkerOfferExpired

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferExpired",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerregistered"></a>Microsoft.Communication.RouterWorkerRegistered

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
    "totalCapacity": 100,
    "queueAssignments": [
      {
        "id": "MyQueueId2",
        "name": "Queue 3",
        "labels": {
          "Language": "en",
          "Product": "Office",
          "Geo": "NA"
        }
      }
    ],
    "labels": {
      "x": "111",
      "y": "111"
    },
    "channelConfigurations": [
      {
        "channelId": "FooVoiceChannelId",
        "capacityCostPerJob": 10
      }
    ]
  },
  "eventType": "Microsoft.Communication.RouterWorkerRegistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| totalCapacity | `int` | ❌ |
| queueAssignments| `List<QueueInfo>` | ❌ |
| 레이블| `Dictionary<string, object>` | ✔️ | | 사용자 입력 기반 |
| channelConfigurations| `List<ChannelConfiguration>` | ❌ |

### <a name="microsoftcommunicationrouterworkerderegistered"></a>Microsoft.Communication.RouterWorkerDeregistered

[이벤트 카탈로그로 돌아가기](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
  },
  "eventType": "Microsoft.Communication.RouterWorkerDeregistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```
**특성 목록**

| attribute | Type | Nullable |Description | 메모 |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
