---
title: Azure Event Grid - 전송되는 이벤트에 사용자 지정 헤더 설정
description: 전송되는 이벤트에 사용자 지정 헤더(또는 전송 속성)를 설정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 643914f5bd7caf89564c856582b6522ff86f6295
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133437307"
---
# <a name="custom-delivery-properties"></a>사용자 지정 전달 속성
이벤트 구독을 사용하면 배달된 이벤트에 포함 되는 HTTP 헤더를 설정할 수 있습니다. 이 기능을 사용하여 대상에 필요한 사용자 지정 헤더를 설정할 수 있습니다. 이벤트 구독을 만들 때 최대 10개의 헤더를 설정할 수 있습니다. 각 헤더 값은 4,096(4K)바이트보다 크지 않아야 합니다.

다음 대상에 배달되는 이벤트에 사용자 지정 헤더를 설정할 수 있습니다.

- Webhook
- Azure Service Bus 토픽 및 큐
- Azure Event Hubs
- 릴레이 하이브리드 연결

Azure Portal에서 이벤트 구독을 만들 때 **전송 속성** 탭을 사용하여 사용자 지정 HTTP 헤더를 설정할 수 있습니다. 이 페이지에서는 고정 및 동적 헤더 값을 설정할 수 있습니다.

## <a name="setting-static-header-values"></a>고정 헤더 값 설정
헤더를 고정 값으로 설정하려면 해당 필드에 헤더 이름 및 값을 제공합니다.

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="전송 속성 - 고정":::

중요한 데이터를 제공하는 경우 **비밀 여부** 를 선택할 수 있습니다. 중요한 데이터는 Azure Portal에 표시되지 않습니다. 

## <a name="setting-dynamic-header-values"></a>동적 헤더 값 설정
수신 이벤트 속성에 따라 헤더 값을 설정할 수 있습니다. JsonPath 구문을 사용하여 수신 이벤트 속성 값을 참조하고 발신 요청의 헤더 값으로 사용합니다. 예를 들어 이벤트 데이터에서 수신 이벤트 속성 **system** 의 값을 사용하여 **Channel** 헤더의 값을 설정하려면 다음과 같이 이벤트 구독을 구성합니다.

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="전송 속성 - 동적":::

## <a name="use-azure-cli"></a>Azure CLI 사용
`az eventgrid event-subscription create` 명령을 사용하여 구독을 만들 때 `--delivery-attribute-mapping` 매개 변수를 사용합니다. 예를 들면 다음과 같습니다.

```azurecli
az eventgrid event-subscription create -n es1 \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1
    --endpoint-type storagequeue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/sa1/queueservices/default/queues/q1 \
    --enable-advanced-filtering-on-arrays true
    --delivery-attribute-mapping staticproperty1 static somestaticvalue2 true 
    --delivery-attribute-mapping staticproperty2 static somestaticvalue3 false 
    --delivery-attribute-mapping dynamicproperty1 dynamic data.key1
```

## <a name="examples"></a>예제
이 섹션에서는 전송 속성을 사용하는 몇 가지 예제를 제공합니다.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>전달자 토큰을 사용하여 인증 헤더 설정(비표준 예제)

웹후크 처리기를 사용하여 요청을 식별하는 인증 헤더에 값을 설정합니다. [Azure Active Directory로 웹후크를 보호](secure-webhook-delivery.md)하지 않는 경우 인증 헤더를 설정할 수 있습니다.

| 헤더 이름   | 헤더 형식 | 헤더 값 |
| :--           | :--         | :--            |
|`Authorization` | 정적 | `BEARER SlAV32hkKG...`|

이제 발신 요청에는 이벤트 구독에 설정된 헤더가 포함되어야 합니다.

```console
POST /home.html HTTP/1.1
Host: acme.com

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> 대상이 웹후크인 경우 인증 헤더를 정의하는 것이 좋습니다. [리소스 ID를 사용하여 구독된 함수](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs, 하이브리드 연결에는 사용하지 않아야 합니다. 해당 대상은 Event Grid에서 사용될 때 자체 인증 체계를 지원하기 때문입니다.

### <a name="service-bus-example"></a>Service Bus 예제
Azure Service Bus에서는 단일 메시지를 보낼 때 다음 메시지 속성을 사용하도록 지원합니다. 

| 헤더 이름 | 헤더 형식 |
| :-- | :-- |
| `MessageId` | 동적 |  
| `PartitionKey` | 정적 또는 동적 |
| `SessionId` | 정적 또는 동적 |
| `CorrelationId` | 정적 또는 동적 |
| `Label` | 정적 또는 동적 |
| `ReplyTo` | 정적 또는 동적 | 
| `ReplyToSessionId` | 정적 또는 동적 |
| `To` |정적 또는 동적 |
| `ViaPartitionKey` | 정적 또는 동적 |

> [!NOTE]
> - `MessageId`의 기본값은 Event Grid 이벤트의 내부 ID입니다. 이를 재정의할 수 있습니다. 예: `data.field`.
> - `SessionId` 또는 `MessageId`만 설정할 수 있습니다. 

### <a name="event-hubs-example"></a>Event Hubs 예제

이벤트 허브 내 특정 파티션에 이벤트를 게시해야 하는 경우 이벤트 구독에서 `ParitionKey` 속성을 정의하여 대상 이벤트 허브 파티션을 식별하는 파티션 키를 지정합니다.

| 헤더 이름 | 헤더 형식 |
| :-- | :-- |
|`PartitionKey` | 정적 |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Azure Storage 큐로 발신되는 이벤트에 TTL(Time to Live) 구성
Azure Storage 큐 대상의 경우 발신 메시지가 Azure Storage 큐로 전송된 후에만 보내는 메시지에 포함될 TTL(Time to Live)을 구성할 수 있습니다. 시간이 제공되지 않는 경우 메시지의 기본 TTL(Time to Live)은 7일입니다. 이벤트가 만료되지 않도록 설정할 수도 있습니다.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="전송 속성 - 스토리지 큐":::

## <a name="next-steps"></a>다음 단계
이벤트 전송에 대한 자세한 내용은 다음 문서를 참조하세요.

- [배달 및 다시 시도](delivery-and-retry.md)
- [웹후크 이벤트 전달](webhook-event-delivery.md)
- [이벤트 필터링](event-filtering.md)
