---
title: Kubernetes의 Azure Event Grid - 이벤트 스키마
description: 이 문서에서는 Kubernetes용 Azure Arc의 Event Grid에서 지원하는 이벤트 스키마를 설명합니다.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: c40507a097cfb341f4bd36155fb284180dbdc88f
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132938061"
---
# <a name="event-schemas-in-event-grid-on-kubernetes"></a>Kubernetes의 Event Grid에 있는 이벤트 스키마
Kubernetes의 Event Grid 모듈에서는 JSON 형식의 이벤트를 허용하고 전송합니다. [클라우드 이벤트 1.0 스키마 사양](https://github.com/cloudevents/spec/blob/v1.0/spec.md)을 지원하며 Event Grid에 이벤트를 게시할 때 사용해야 하는 스키마입니다. 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]



## <a name="cloudevent-schema"></a>CloudEvent 스키마
[CloudEvents](https://cloudevents.io/)는 이벤트 데이터를 설명하는 공개 사양입니다. 이벤트를 게시하고 사용하는 데 필요한 일반 이벤트 스키마를 제공하여 상호 운용성을 간소화합니다. 필수 컨텍스트 특성에 대한 정보는 [CloudEvents 사양](https://github.com/cloudevents/spec/blob/main/cloudevents/formats/json-format.md#3-envelope)을 참조하세요.

## <a name="example--event-using-cloudevents-schema"></a>예 — CloudEvents 스키마를 사용하는 이벤트

```json
[{
      "specversion": "1.0",
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

## <a name="next-steps"></a>다음 단계
Kubernetes용 Azure Arc의 Event Grid에서 지원하는 대상 및 처리기에 대해 알아보려면 [Kubernetes의 Event Grid - 이벤트 처리기](event-handlers.md)를 참조하세요.
