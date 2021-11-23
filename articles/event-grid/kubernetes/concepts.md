---
title: Kubernetes의 Azure Event Grid - 개념
description: 이 문서에서는 Azure Arc(미리 보기)를 사용하는 Kubernetes의 Azure Event Grid에 대한 핵심 개념을 설명합니다.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: eabfe62972dc8e69f700e70e55bc05f5ab14c19d
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940413"
---
# <a name="event-grid-on-kubernetes---concepts"></a>Kubernetes의 Event Grid - 개념
이 문서에서는 Azure Arc(미리 보기)를 사용하는 Kubernetes의 Azure Event Grid에 대한 주요 개념을 설명합니다.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

## <a name="events"></a>이벤트
이벤트는 소프트웨어 시스템의 작업에 대한 팩트를 알리는 데이터 레코드입니다. 일반적으로 이벤트는 시스템에서 발생한 신호 또는 시스템에서 관찰한 신호로 인한 상태 변경을 알립니다. 이벤트에는 다음과 같은 두 가지 유형의 정보가 포함됩니다. 

- [이벤트 데이터](https://github.com/cloudevents/spec/blob/main/cloudevents/spec.md#event-data) - 상태 변경의 발생을 나타냅니다. 
- [컨텍스트 특성](https://github.com/cloudevents/spec/blob/main/cloudevents/spec.md#context-attributes) - 이벤트 발생에 대한 컨텍스트 정보를 제공합니다.     

    이벤트 데이터 및 컨텍스트 특성을 모두 이벤트 필터링에 사용할 수 있습니다. 

Kubernetes의 Event Grid는 [CloudEvents](https://github.com/cloudevents/spec/tree/master) 스키마 사양을 지원합니다. CloudEvents 스키마를 사용하는 이벤트의 예는 다음과 같습니다. Event Grid는 최대 1MB 크기의 이벤트를 지원합니다.

```json
[{
       "specVersion": "1.0",
       "type" : "orderCreated",
       "source": "myCompanyName/us/webCommerceChannel/myOnlineCommerceSiteBrandName",
       "id" : "eventId-n",
       "time" : "2020-12-25T20:54:07+00:00",
       "subject" : "account/acct-123224/order/o-123456",
       "dataSchema" : "1.0",
       "data" : {
          "orderId" : "123",
          "orderType" : "PO",
          "reference" : "https://www.myCompanyName.com/orders/123"
      }
}]
```

## <a name="source"></a>원본
원본 특성은 이벤트가 발생한 컨텍스트를 설명합니다. 원본은 이벤트의 주관자일 수 있습니다. 그러나 어떤 경우에는 이벤트를 만들고 게시하는 생산자가 있습니다. 그리고 이러한 생산자는 원본과 다릅니다. 편의상 이 문서에서는 원본이 이벤트의 생산자라고 가정합니다. 

각 이벤트 원본은 하나 이상의 이벤트 유형 이벤트를 생성합니다. 이벤트의 원본인 애플리케이션은 상태 변경을 알리기 위해 관련 이벤트 집합을 정의합니다. 모든 이벤트에는 이벤트의 원본, 이벤트가 발생한 시간 및 고유 식별자와 같은 일반적인 정보가 있습니다. 또한 모든 이벤트에는 특정 유형의 이벤트에만 관련된 특정 정보도 있습니다. 최대 크기가 1MB인 이벤트에 대한 지원은 현재 미리 보기 상태입니다.

이벤트에 포함된 속성은 [CloudEvents 스키마](event-schemas.md#cloudevent-schema)를 참조하세요.

## <a name="publishers"></a>게시자
이벤트 게시자는 이벤트 구독자에게 배달할 이벤트를 Event Grid에 보내는 애플리케이션 또는 시스템입니다.

## <a name="topics"></a>토픽
토픽은 게시자가 Event Grid에 이벤트를 보내는 엔드포인트를 공개하는 일종의 입력 채널입니다.

토픽은 관련 이벤트의 컬렉션에 사용될 수 있습니다. 관련된 이벤트의 범주마다 토픽을 만들 수 있습니다. 원본은 일반적으로 밀접하게 관련된 이벤트 유형 집합("MyApp.OrderCreated", "MyApp.OderDeleted", "MyApp.OrderRejected" 등)과 연결되기 때문에 어떤 경우에는 원본을 사용하여 이벤트를 범주로 구성할 수 있습니다. 

사용자 계정을 관리하고 주문을 처리하는 것과 관련된 이벤트를 보내는 애플리케이션을 예로 들어 보겠습니다. 이벤트 구독자는 두 범주의 이벤트를 사용하는 데 관심이 없을 수 있습니다. 두 개의 사용자 지정 토픽을 만들고 이벤트 처리기가 관심 있는 토픽을 구독할 수 있도록 하세요. 소규모 솔루션의 경우 모든 이벤트를 단일 토픽으로 전송할 수도 있습니다. 

## <a name="event-subscribers"></a>이벤트 구독자
이벤트 구독자는 Event Grid에서 이벤트를 배달하는 엔드포인트를 노출하는 마이크로서비스와 같은 소프트웨어 시스템입니다. 

## <a name="event-subscriptions"></a>이벤트 구독
이벤트 구독은 수신하려는 토픽의 이벤트(이벤트 필터링) 및 보낼 위치(이벤트 라우팅)를 Event Grid에 알려줍니다. 이벤트 구독을 만들 때는 이벤트 처리용 엔드포인트를 제공합니다. 이벤트 구독에서 필터 절을 구성하여 엔드포인트에 배달하려는 이벤트를 선택할 수 있습니다. 

## <a name="event-handlers"></a>이벤트 처리기
이벤트 처리기는 이벤트가 전송되는 엔드포인트를 노출하는 소프트웨어 시스템입니다. 처리기는 이벤트를 수신하고 이벤트를 처리하는 작업을 수행합니다. Event Grid는 여러 가지 처리기 유형을 지원합니다. Kubernetes 또는 Azure에서 호스트되는 지원 Azure 서비스 또는 호스트되는 모든 위치에 웹후크(엔드포인트)를 노출하는 고유한 솔루션을 처리기로 사용할 수 있습니다. 처리기의 형식에 따라 Event Grid는 이벤트의 배달을 보장하는 다양한 메커니즘을 따릅니다. 대상 이벤트 처리기가 HTTP 웹후크인 경우 처리기가 상태 코드 200 – OK를 반환할 때까지 이벤트가 다시 시도됩니다. 자세한 내용은 [이벤트 처리기](event-handlers.md)를 참조하세요.

## <a name="sas-authentication"></a>SAS 인증
Kubernetes의 Event Grid는 토픽에 이벤트를 게시하기 위한 SAS 키 기반 인증을 제공합니다.

## <a name="event-delivery"></a>이벤트 전달
Kubernetes의 Event Grid는 안정적인 배달 및 재시도 메커니즘을 제공합니다. Event Grid는 이벤트 처리기 엔드포인트에서 이벤트가 수신되었는지 확인할 수 없는 경우 이벤트를 다시 배달합니다. 자세한 내용은 [Event Grid 메시지 배달 및 재시도](delivery-retry.md)를 참조하세요.

## <a name="batch-event-publishing"></a>Batch 이벤트 게시
토픽을 사용하는 경우 이벤트를 항상 배열에 게시해야 합니다. 처리량이 낮은 시나리오의 경우 배열은 한 개의 이벤트만 가집니다. 대량 사용 사례의 경우에는 게시당 여러 이벤트를 함께 일괄 처리하여 효율성을 높이는 것이 좋습니다. 일괄 처리의 최대 크기는 1MB입니다. 각 이벤트도 1MB보다 크면 안 됩니다. 자세한 내용은 [Batch 이벤트 배달](batch-event-delivery.md)을 참조하세요.

## <a name="event-grid-on-kubernetes-components"></a>Kubernetes의 Event Grid 구성 요소

- **Event Grid 운영자** 는 [운영자 패턴](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)을 구현합니다. Kubernetes API 서버로 전송된 제어 평면 요청으로 인한 Event Grid 리소스의 상태 변경을 감시합니다. Event Grid 리소스의 상태에 영향을 주는 요청이 있는 경우 Event Grid 운영자는 해당 상태를 Event Grid Broker와 동기화합니다.
- **Event Grid broker** 는 제어 평면 작업과 데이터 평면 작업으로 사용됩니다.

   제어 평면 서비스로서 Event Grid의 상태를 Event Grid 운영자가 알려준 원하는 상태로 전환하는 일을 담당합니다. 예를 들어 새 토픽 생성 요청이 전송되면 요청을 수행하고 서비스 메타데이터를 업데이트합니다.

   데이터 평면 서비스로서 모든 이벤트 게시 요청을 처리하고 이벤트 구독에 구성된 대상에 이벤트를 배달합니다.

## <a name="next-steps"></a>다음 단계
시작하려면 [토픽 및 구독 만들기](create-topic-subscription.md)를 참조하세요.
