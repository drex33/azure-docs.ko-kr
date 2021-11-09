---
title: SaaS 서비스에서 webhook 구현 | Microsoft 상업적 marketplace
description: Api 버전 2를 사용 하 여 SaaS 서비스에서 webhook을 구현 하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: 6cf50327719b434cda7d5df4e3df99b9d96a1dcd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064461"
---
# <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS 서비스에서 webhook 구현

파트너 센터에서 불가능 SaaS 제안을 만들 때 파트너는 HTTP 끝점으로 사용할 **연결 webhook** URL을 제공 합니다.  이 webhook는 Microsoft가 Microsoft 쪽에서 발생하는 다음 이벤트를 게시자 쪽에 알리기 위해 POST HTTP 호출을 사용하여 호출합니다.

* SaaS 구독이 *구독 취소됨* 상태인 경우:
    * ChangePlan
    * ChangeQuantity
    * 갱신
    * 일시 중지됨
    * 구독 취소
* SaaS 구독이 *일시 중단됨* 상태인 경우:
    * 복구
    * 구독 취소

게시자는 SaaS 구독 상태가 Microsoft 쪽과 동일하게 유지되도록 SaaS 서비스에서 webhook를 구현해야 합니다.  webhook 알림에 따라 작업을 수행하기 전에 작업 가져오기 API를 호출하여 webhook 호출 및 페이로드 데이터의 유효성을 검사하고 권한을 부여하려면 SaaS 서비스가 필요합니다.  webhook 호출이 처리되는 즉시 게시자는 HTTP 200을 Microsoft에 반환해야 합니다.  이 값은 게시자가 webhook 호출을 성공적으로 받았다는 것을 확인합니다.

> [!IMPORTANT]
> webhook URL 서비스는 연중 무휴 실행되어야 하며, 항상 Microsoft에서 새로운 호출을 받을 준비가 되어 있어야 합니다.  Microsoft에는 웹 후크 호출에 대 한 다시 시도 정책이 있지만 500 (8 시간 넘게 다시 시도) 게시자가 통화를 수락 하지 않고 응답을 반환 하는 경우 웹 후크를 알리는 작업이 결국 Microsoft 쪽에서 실패 합니다.

*구입 이벤트의 Webhook 페이로드 예:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": "25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

*구독 reinstatement 이벤트의 Webhook 페이로드 예:*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": "20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "InProgress"
}
```

*갱신 이벤트의 Webhook 페이로드 예:*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer1 ",
  "planId": "silver",
  "quantity": "25",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Renew",
  "status": "Success"
}
```

## <a name="development-and-testing"></a>개발 및 테스트

개발 프로세스를 시작하려면 게시자 쪽에서 더미 API 응답을 만드는 것이 좋습니다.  이러한 응답은 이 문서에 제공된 샘플 응답을 기반으로 할 수 있습니다.

게시자는 종합적인 테스트를 수행할 준비가 되면 다음을 수행합니다.

* 제한된 미리 보기 대상에 SaaS 제품을 게시하고 미리 보기 단계를 유지합니다.
* 테스트하는 동안 실제로 비용 청구를 트리거하는 일이 없도록 플랜 가격을 0으로 설정합니다.  또 다른 옵션은 0이 아닌 가격을 설정하고 24시간 이내에 모든 테스트 구매를 취소하는 것입니다.
* 모든 흐름이 처음부터 끝까지 호출되는지 확인하여 실제 고객 시나리오를 시뮬레이션합니다.
* 구매부터 청구까지 전체 흐름을 테스트하려는 파트너는 0이 아닌 가격이 책정된 제품으로 테스트를 수행하면 됩니다.  그러면 구매 대금이 청구되고 청구서가 생성됩니다.

제품이 게시되는 위치에 따라 Azure Portal 또는 Microsoft AppSource 사이트에서 구매 흐름을 트리거할 수 있습니다.

*플랜 변경*, *수량 변경* 및 *구독 취소* 작업은 게시자 쪽에서 테스트합니다.  Microsoft 쪽에서는 *구독 취소* 를 Azure Portal과 관리 센터(Microsoft AppSource 구매가 관리되는 포털) 모두에서 트리거할 수 있습니다.  *수량 및 플랜 변경* 은 관리 센터에서만 트리거할 수 있습니다.

## <a name="get-support"></a>지원 받기

게시자 지원 옵션은 [파트너 센터의 상업용 Marketplace 프로그램 지원](../support.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

상업용 Marketplace의 SaaS 제품에 대한 추가 옵션은 [상업용 Marketplace 서비스 계량 API](../marketplace-metering-service-apis.md)를 참조하세요.

[다른 프로그래밍 언어 및 샘플에 대한 클라이언트](https://github.com/microsoft/commercial-marketplace-samples)를 검토하고 사용해 보세요.
