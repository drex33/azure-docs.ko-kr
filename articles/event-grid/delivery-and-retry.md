---
title: Azure Event Grid 배달 및 다시 시도
description: Azure Event Grid에서 이벤트를 배달하는 방법 및 배달되지 않은 메시지를 처리하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a6055a99e717dd379dc6bd43411c73456bdaede8
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730334"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 메시지 배달 및 다시 시도
Event Grid는 지속성이 있는 배달을 제공합니다. 일치하는 구독마다 즉각적으로 각 메시지를 **한 번 이상** 전송하려고 시도합니다. 구독자 엔드포인트에서 이벤트 수신을 승인하지 않거나 오류가 있는 경우 Event Grid는 고정된 [다시 시도 일정](#retry-schedule) 및 [재시도 정책](#retry-policy)에 따라 전송을 다시 시도합니다. 기본적으로 Event Grid 모듈은 한 번에 하나의 이벤트를 구독자에게 전송합니다. 하지만 페이로드는 단일 이벤트가 포함된 배열입니다.

> [!NOTE]
> Event Grid는 이벤트 배달 순서를 보장하지 않으므로 구독자가 잘못된 순서로 메시지를 받을 수 있습니다. 

## <a name="retry-schedule"></a>다시 시도 일정
EventGrid는 이벤트 배달 시도에 대한 오류를 수신하면 해당 오류 유형에 따라 배달을 다시 시도하거나, 이벤트를 배달 못한 편지로 처리하거나, 이벤트를 삭제할지 여부를 결정합니다. 

구독한 엔드포인트에서 반환된 오류가 다시 시도로 해결할 수 없는 구성 관련 오류인 경우(예: 엔드포인트가 삭제된 경우) EventGrid는 이벤트를 배달 못한 편지로 처리하고, 배달 못한 편지가 구성되지 않은 경우에는 이벤트를 삭제합니다.

다음 표에서는 다시 시도가 발생하지 않는 엔드포인트와 오류의 유형을 설명합니다.

| 엔드포인트 유형 | 오류 코드 |
| --------------| -----------|
| Azure 리소스 | 400 잘못된 요청, 413 요청 엔터티가 너무 큼, 403 사용할 수 없음 | 
| 웹후크 | 400 잘못된 요청, 413 요청 엔터티가 너무 큼, 403 사용할 수 없음, 404 찾을 수 없음, 401 권한 없음 |
 
> [!NOTE]
> 엔드포인트에 배달 못한 편지가 구성되지 않은 경우 위의 오류가 발생하면 이벤트가 삭제됩니다. 이러한 종류의 이벤트가 삭제되지 않도록 하려면 배달 못한 편지를 구성하는 것이 좋습니다.

구독한 엔드포인트에서 반환한 오류가 위의 목록에 없는 경우 EventGrid는 아래에 설명된 정책을 사용하여 재시도를 수행합니다.

Event Grid는 메시지 전달 후 30초 동안 응답을 기다립니다. 30초 후 엔드포인트가 응답하지 않으면 메시지가 재시도를 위해 큐에 대기합니다. Event Grid는 이벤트 배달에 대해 지수 백오프 재시도 정책을 사용합니다. Event Grid는 다음 일정에 따라 가장 적합한 방식으로 배달을 다시 시도합니다.

- 10초
- 30초
- 1분
- 5분
- 10분
- 30분
- 1시간
- 3시간
- 6시간
- 12시간~최대 24시간마다


엔드포인트가 3분 이내에 응답하는 경우, Event Grid는 가장 적합한 방식으로 재시도 큐에서 이벤트를 제거하려고 시도하지만 중복 항목이 계속 수신될 수 있습니다.

Event Grid는 모든 재시도 단계에 작은 임의 설정을 추가하고, 엔드포인트가 일관되게 비정상 상태이거나, 오랜 기간 동안 다운되거나, 과부하가 발생한 것처럼 보이는 경우 특정 재시도를 선택적으로 건너뛸 수 있습니다.

## <a name="retry-policy"></a>재시도 정책
다음 두 가지 구성을 사용하여 이벤트 구독을 만들 때 재시도 정책을 사용자 지정할 수 있습니다. 재시도 정책 한도 중 하나에 도달하면 이벤트가 삭제됩니다. 

- **최대 시도 횟수** - 값은 1에서 30 사이의 정수여야 합니다. 기본값은 30입니다.
- **이벤트 TTL(Time-to-Live)** - 값은 1에서 1440 사이의 정수여야 합니다. 기본값은 1440분입니다.

이러한 설정을 구성하는 샘플 CLI 및 PowerShell 명령은 [다시 시도 정책 설정](manage-event-delivery.md#set-retry-policy)을 참조하세요.

## <a name="output-batching"></a>출력 일괄 처리 
Event Grid는 기본적으로 각 이벤트를 구독자에게 개별적으로 보냅니다. 구독자는 단일 이벤트로 배열을 받습니다. 처리량이 높은 시나리오에서 향상된 HTTP 성능으로 배달하기 위해 이벤트를 일괄 처리하도록 Event Grid를 구성할 수 있습니다. 일괄 처리는 기본적으로 해제되어 있으며 구독별로 설정할 수 있습니다.

### <a name="batching-policy"></a>일괄 처리 정책
일괄 배달에는 두 가지 설정이 있습니다.

* **일괄 처리당 최대 이벤트 수** - Event Grid에서 일괄 처리당 배달할 최대 이벤트 수입니다. 이 숫자는 절대로 초과되지 않지만 게시할 때 다른 이벤트를 사용할 수 없는 경우에는 더 적은 이벤트가 배달될 수 있습니다. 사용할 수 있는 이벤트 수가 더 적으면 Event Grid가 이벤트를 지연시켜 일괄 처리를 만들 수 없습니다. 1에서 5,000 사이여야 합니다.
* **기본 일괄 처리 크기(KB)** - 일괄 처리 크기의 목표 상한(KB)입니다. 최대 이벤트와 마찬가지로, 게시할 때 더 많은 이벤트를 사용할 수 없는 경우 일괄 처리 크기가 더 작아질 수 있습니다. 단일 이벤트가 기본 크기보다 큰 *경우*, 일괄 처리가 기본 일괄 처리 크기보다 클 수 있습니다. 예를 들어 기본 크기가 4KB인데 10KB의 이벤트가 Event Grid로 푸시되더라도 10KB 이벤트는 삭제되지 않고 자체 일괄 처리로 배달됩니다.

포털, CLI, PowerShell 또는 SDK를 통해 이벤트 구독별로 구성된 일괄 배달

### <a name="batching-behavior"></a>일괄 처리 동작

* 모두 성공 또는 실패

  Event Grid는 모두 성공 또는 실패 의미 체계로 작동합니다. 일괄 처리 전송에서는 일부 성공이 지원되지 않습니다. 구독자는 합리적으로 60초 이내에 처리할 수 있는 만큼의 일괄 처리당 이벤트만 요청하도록 주의해야 합니다.

* 최적 일괄 처리

  일괄 처리 정책 설정은 일괄 처리 동작을 엄격하게 제한하지 않으며 가장 효율적인 방식으로 적용됩니다. 이벤트율이 낮은 경우 일괄 처리 크기가 일괄 처리당 요청된 최대 이벤트 수보다 작은 경우가 많습니다.

* 기본적으로 해제로 설정

  기본적으로 Event Grid는 전송 요청마다 이벤트를 하나만 추가합니다. 일괄 처리를 설정하는 방법은 이벤트 구독 JSON에서 이 문서 앞부분에 언급된 설정 중 하나를 지정하는 것입니다.

* 기본값

  이벤트 구독을 만들 때 설정(일괄 처리당 최대 이벤트 수 및 대략적인 일괄 처리 크기(킬로바이트))을 둘 다 지정할 필요는 없습니다. 설정이 하나만 지정되면 Event Grid에서 (구성 가능한) 기본값을 사용합니다. 기본값과 기본값을 재정의하는 방법은 다음 섹션을 참조하세요.

### <a name="azure-portal"></a>Azure Portal: 
![일괄 배달 설정](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
이벤트 구독을 만들 때 다음 매개 변수를 사용 합니다. 

- **max-events-per-batch** - 일괄 처리의 최대 이벤트 수입니다. 값은 1에서 5000 사이의 숫자여야 합니다.
- **preferred-batch-size-in-kilobytes** - 기본 일괄 처리 크기(KB)입니다. 값은 1에서 1024 사이의 숫자여야 합니다.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Event Grid에서 Azure CLI를 사용하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 웹 엔드포인트로 스토리지 이벤트 라우팅](../storage/blobs/storage-blob-event-quickstart.md)을 참조하세요.


## <a name="delayed-delivery"></a>지연된 배달
엔드포인트에 배달 오류가 발생하면 Event Grid는 해당 엔드포인트에 대한 배달 및 이벤트 재시도를 지연시키기 시작합니다. 예를 들어 엔드포인트에 게시된 처음 10개의 이벤트가 실패할 경우, Event Grid는 엔드포인트에 문제가 발생한 것으로 가정하고, 모든 후속 재시도 *및 신규* 배달을 얼마 동안(경우에 따라 최대 몇 시간) 지연시킵니다.

지연된 배달의 기능상 목적은 비정상 엔드포인트와 Event Grid 시스템을 보호하는 것입니다. 비정상 엔드포인트에 대한 배달 백오프 및 지연 없이 Event Grid의 재시도 정책 및 볼륨 기능을 사용하면 시스템에 쉽게 과부하가 발생할 수 있습니다.

## <a name="dead-letter-events"></a>배달 못한 편지 이벤트
Event Grid가 일정 시간 안에 또는 일정 횟수의 이벤트 배달 시도 후 이벤트를 배달할 수 없는 경우, 미배달 이벤트를 스토리지 계정에 보낼 수 있습니다. 이 프로세스를 **배달 못한 편지 처리** 라고 합니다. Event Grid는 **다음 조건 중 하나** 가 충족되면 이벤트를 배달 못한 편지로 처리합니다. 

- 이벤트가 **TTL(Time to Live)** 기간 내에 배달되지 않은 경우 
- 이벤트 전달 **시도 횟수** 가 한도를 초과한 경우

조건 중 하나가 충족되면 이벤트가 삭제되거나 배달 못한 편지로 처리됩니다.  기본적으로 Event Grid에서는 배달 못한 편지를 켜지 않습니다. 이 기능을 사용하려면 이벤트 구독을 만들 때 전송되지 않은 이벤트를 보유할 스토리지 계정을 지정해야 합니다. 이 스토리지 계정에서 이벤트를 끌어와 전송을 해결합니다.

Event Grid가 모든 재시도 시도 횟수를 시도한 경우 이벤트를 배달 못한 편지로 보냅니다. Event Grid가 400(잘못된 요청) 또는 413(요청 엔터티가 너무 큼) 응답 코드를 수신하는 경우, 즉시 이벤트의 배달 못한 편지 처리를 예약합니다. 이 응답 코드는 이벤트 전달이 실패하지 않음을 나타냅니다.

TTL(Time to Live) 만료는 다음에 예약된 배달 시도 시에만 확인됩니다. 따라서 다음에 예약된 배달 시도 전에 TTL(Time to Live)이 만료되는 경우에도 다음 배달 시점에만 이벤트 만료가 확인된 후 나중에 배달 못한 편지로 처리됩니다. 

이벤트를 배달하기 위한 마지막 시도 및 배달 못한 편지 위치로 이벤트를 배달하는 경우, 그 사이에 5분의 지연이 발생합니다. 이 지연은 Blob Storage 작업 수를 줄이기 위함입니다. 배달 못한 편지 위치를 4시간 동안 사용할 수 없는 경우 이벤트가 삭제됩니다.

배달 못한 편지 위치를 설정하기 전에 컨테이너를 포함하는 스토리지 계정이 있어야 합니다. 이벤트 구독을 만들 때 이 컨테이너에 대한 엔드포인트를 제공합니다. 엔드포인트는 다음과 같은 형식입니다. `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

이벤트가 배달 못한 편지 위치로 전송된 경우 알림을 받을 수 있습니다. Event Grid를 사용하여 전송되지 않은 이벤트에 응답하려면 배달 못한 편지 Blob Storage에 대한 [이벤트 구독을 만듭니다](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 배달 못한 편지 Blob Storage가 전송되지 않은 이벤트를 수신할 때마다 Event Grid에서 처리기에 알립니다. 처리기는 전송되지 않은 이벤트를 조정하기 위해 수행할 작업으로 응답합니다. 배달 못한 편지 위치 및 재시도 정책을 설정하는 예제는 [배달 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.

## <a name="delivery-event-formats"></a>배달 이벤트 형식
이 섹션에서는 다양한 배달 스키마 형식(Event Grid 스키마, CloudEvents 1.0 스키마 및 사용자 지정 스키마)의 이벤트 및 배달 못한 편지로 처리된 이벤트에 대한 예제를 제공합니다. 이러한 형식에 대한 자세한 내용은 [Event Grid 스키마](event-schema.md) 및 [클라우드 이벤트 1.0 스키마](cloud-event-schema.md) 문서를 참조하세요. 

### <a name="event-grid-schema"></a>Event Grid 스키마

#### <a name="event"></a>이벤트 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>배달 못한 편지 이벤트

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1.0 스키마

#### <a name="event"></a>이벤트

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>배달 못한 편지 이벤트

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>사용자 지정 스키마

#### <a name="event"></a>이벤트

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>배달 못한 편지 이벤트
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>메시지 배달 상태

Event Grid는 HTTP 응답 코드를 사용하여 이벤트의 수신을 승인합니다. 

### <a name="success-codes"></a>성공 코드

Event Grid는 다음 HTTP 응답 코드 **만** 성공한 배달로 간주합니다. 다른 모든 상태 코드는 실패한 배달로 간주되며 적절하게 다시 시도하거나 배달 못한 편지로 처리됩니다. 성공적인 상태 코드를 수신하면 Event Grid에서 배달이 완료된 것으로 간주합니다.

- 200 정상
- 201 생성됨
- 202 수락됨
- 203 신뢰할 수 없는 정보
- 204 콘텐츠 없음

### <a name="failure-codes"></a>실패 코드

위의 집합(200-204)에 없는 다른 모든 코드는 오류로 간주되며 필요한 경우 다시 시도됩니다. 일부에는 구체적인 다시 시도 정책이 연결되어 있고(아래 참조) 나머지는 모두 표준 지수 백오프 모델을 따릅니다. Event Grid 아키텍처의 고도로 병렬화된 특성으로 인해 재시도 동작이 비결정적이라는 사실을 유념해야 합니다. 

| 상태 코드 | 다시 시도 동작 |
| ------------|----------------|
| 400 잘못된 요청 | 다시 시도 안 함 |
| 401 권한 없음 | Azure 리소스 엔드포인트에 대해 5분 이상 후 다시 시도 |
| 403 사용할 수 없음 | 다시 시도 안 함 |
| 404 찾을 수 없음 | Azure 리소스 엔드포인트에 대해 5분 이상 후 다시 시도 |
| 408 요청 시간 초과 | 2분 이상 후 다시 시도 |
| 413 요청 엔터티가 너무 큼 | 다시 시도 안 함 |
| 503 서비스를 사용할 수 없음 | 30초 이상 후 다시 시도 |
| 나머지 | 10초 이상 후 다시 시도 |

## <a name="custom-delivery-properties"></a>사용자 지정 배달 속성
이벤트 구독을 사용하면 배달된 이벤트에 포함 되는 HTTP 헤더를 설정할 수 있습니다. 이 기능을 사용하여 대상에 필요한 사용자 지정 헤더를 설정할 수 있습니다. 이벤트 구독을 만들 때 최대 10개의 헤더를 설정할 수 있습니다. 각 헤더 값은 4,096(4K)바이트보다 크지 않아야 합니다. 다음 대상에 배달되는 이벤트에 사용자 지정 헤더를 설정할 수 있습니다.

- Webhook
- Azure Service Bus 토픽 및 큐
- Azure Event Hubs
- 릴레이 하이브리드 연결

자세한 내용은 [사용자 지정 배달 속성](delivery-properties.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 상태를 보려면 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 이벤트 전달 옵션을 사용자 지정하려면 [전송 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.
