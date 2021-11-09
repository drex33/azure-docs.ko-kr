---
title: 계량 서비스 API - Microsoft 상업용 마켓플레이스
description: 사용량 이벤트 API를 사용하여 Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품에 대한 사용량 이벤트를 내보낼 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/15/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: f420934489c6e5e65b050ce30ded8fb922dfdfe8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061365"
---
# <a name="marketplace-metered-billing-apis"></a>Marketplace 요금제 청구 API

요금제 청구 API는 게시자가 파트너 센터에 게시할 제안에 대한 사용자 지정 계량 차원을 만들 때 사용해야 합니다. 사용 이벤트를 내보내기 위해 사용자 지정 차원과 하나 이상의 플랜이 있는 모든 구매 제품에는 요금제 청구 API와의 통합이 필요합니다.

> [!IMPORTANT]
> 코드의 사용량을 추적하고 기본 요금을 초과하는 사용량에 대해서만 사용 이벤트를 Microsoft로 보내야 합니다.

SaaS의 사용자 지정 계량 차원을 만드는 방법에 대한 자세한 내용은 [SaaS 요금제 청구](partner-center-portal/saas-metered-billing.md)를 참조하세요.

관리 앱 플랜으로 Azure 애플리케이션 제품의 사용자 지정 계량 차원을 만드는 방법에 대한 자세한 내용은 [Azure 애플리케이션 제품 설정 세부 정보 구성](azure-app-offer-setup.md#configure-your-azure-application-offer-setup-details)을 참조하세요.

## <a name="enforcing-tls-12-note"></a>TLS 1.2 적용 참고 사항

TLS 버전 1.2 버전은 HTTPS 통신을 위한 최소 버전으로 적용됩니다. 코드에서 이 TLS 버전을 사용해야 합니다. TLS 버전 1.0 및 1.1은 더 이상 사용되지 않으며 연결 시도가 거부됩니다.

## <a name="metered-billing-single-usage-event"></a>요금제 청구 단일 사용 이벤트

특정 고객이 구매한 플랜의 활성 리소스(구독)에 대해 사용 이벤트를 내보내기 위해 게시자가 사용 이벤트 API를 호출해야 합니다. 사용 이벤트는 제품을 게시할 때 게시자가 정의한 계획의 각 사용자 지정 차원에 대해 별도로 내보내집니다.

달력상의 각 시간에 대해 하나의 사용 이벤트만 내보낼 수 있습니다. 예를 들어 오늘 오전 8시 15분에 하나의 사용 이벤트를 내보낼 수 있습니다. 이 이벤트가 수락되면 오늘 오전 9시부터 다음 사용 이벤트가 수락됩니다. 오늘 오전 8시 15분에서 8시 59분 59초 사이에 추가 이벤트를 보내면 중복으로 간주되어 거부됩니다. 1시간 동안 사용된 모든 단위를 누적한 다음, 단일 이벤트에서 내보내야 합니다.

리소스당 달력상의 각 시간에 대해 하나의 사용 이벤트만 내보낼 수 있습니다. 한 시간에 두 개 이상의 단위가 사용된 경우에는 해당 시간에 사용된 모든 단위를 누적한 다음, 단일 이벤트에서 내보냅니다. 사용 이벤트는 지난 24시간 동안만 내보낼 수 있습니다. 오전 8시에서 8시 59분 59초 사이에 아무 때나 사용 이벤트를 내보내고(수락됨) 같은 날 오전 8시에서 8시 59분 59초에 추가 이벤트를 보내면 중복으로 간주되어 거부됩니다.

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

| 매개 변수 | 권장          |
| ---------- | ---------------------- |
| `ApiVersion` | Use 2018-08-31. |
| | |

*요청 헤더:*

| Content-type       | `application/json` 사용  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`   | 이 API 호출을 수행하는 ISV를 식별하는 고유한 액세스 토큰입니다. 다음에 설명된 대로 게시자가 토큰 값을 검색하는 경우 형식은 `"Bearer <access_token>"`입니다. <br> <ul> <li> [HTTP POST를 사용하여 토큰 가져오기](partner-center-portal/pc-saas-registration.md#get-the-token-with-an-http-post)의 SaaS </li> <li> [인증 전략](marketplace-metering-service-authentication.md)의 관리형 애플리케이션 </li> </ul> |
| | |

*요청 본문 예제:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId`는 SaaS 앱과 사용자 지정 요금제를 내보내는 관리 앱에 대해 서로 다른 의미를 갖습니다. 

Azure 애플리케이션 관리형 앱 플랜의 경우 `resourceId`는 관리 앱 `resource group Id`입니다. 이를 인출하는 예제 스크립트는 [Azure 관리 ID 토큰 사용](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)에서 찾을 수 있습니다. 

SaaS 제품의 경우 `resourceId`는 SaaS 구독 ID입니다. SaaS 구독에 대한 자세한 내용은 [구독 나열](partner-center-portal/pc-saas-fulfillment-subscription-api.md#get-list-of-all-subscriptions)을 참조하세요.

### <a name="responses"></a>응답

코드: 200<br>
OK. 추가 처리 및 청구를 위해 Microsoft 측에서 사용량 내보내기를 수락하고 기록했습니다.

응답 페이로드 예제: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

코드: 400 <br>
잘못된 요청입니다.

* 제공된 요청 데이터가 누락되었거나 잘못되었습니다.
* `effectiveStartTime`이 지난 24시간을 초과했습니다. 이벤트가 만료되었습니다.
* SaaS 구독이 구독 상태가 아닙니다.

응답 페이로드 예제: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

코드: 403<br>

사용할 수 없습니다. 권한 부여 토큰이 제공되지 않았거나, 잘못 되었거나, 만료되었습니다.  또는 요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 구독에 액세스하려고 합니다.

코드: 409<br>
충돌. 지정된 리소스 ID, 유효 사용 날짜 및 시간에 대한 사용 이벤트가 이미 보고되었습니다.

응답 페이로드 예제: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>요금제 청구 일괄 처리 사용 이벤트

일괄 처리 사용 이벤트 API를 사용하면 구매한 둘 이상의 특정 리소스에 대한 사용 이벤트를 한 번에 내보낼 수 있습니다. 또한 달력 시간이 다르면 동일한 리소스에 대해 여러 사용 이벤트를 내보낼 수 있습니다. 단일 일괄 처리의 최대 이벤트 수는 25개입니다.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

| 매개 변수  | 권장     |
| ---------- | -------------------- |
| `ApiVersion` | Use 2018-08-31. |

*요청 헤더:*

| Content-type       | `application/json` 사용       |
| ------------------ | ------ |
| `x-ms-requestid`     | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`      | 이 API 호출을 수행하는 ISV를 식별하는 고유한 액세스 토큰입니다. 다음에 설명된 대로 게시자가 토큰 값을 검색하는 경우 형식은 `Bearer <access_token>`입니다. <br> <ul> <li> [HTTP POST를 사용하여 토큰 가져오기](partner-center-portal/pc-saas-registration.md#get-the-token-with-an-http-post)의 SaaS </li> <li> [인증 전략](./marketplace-metering-service-authentication.md)의 관리형 애플리케이션 </li> </ul> |
| | |

>[!NOTE]
>요청 본문에서 리소스 식별자는 SaaS 앱 및 사용자 지정 미터를 내보내는 Azure 관리형 앱에 대해 서로 다른 의미를 줍니다. SaaS 앱의 리소스 식별자는 `resourceID` 입니다. Azure 애플리케이션 Managed Apps 계획의 리소스 식별자는 `resourceUri` 입니다.

SaaS 제품의 경우 `resourceId`는 SaaS 구독 ID입니다. SaaS 구독에 대한 자세한 내용은 [구독 나열](partner-center-portal/pc-saas-fulfillment-subscription-api.md#get-list-of-all-subscriptions)을 참조하세요.

*SaaS 앱에 대한 요청 본문 예제:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

Azure 애플리케이션 관리형 앱 플랜의 경우 `resourceUri`는 관리 앱 `resource group Id`입니다. 이를 인출하는 예제 스크립트는 [Azure 관리 ID 토큰 사용](marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)에서 찾을 수 있습니다. 

*Azure 애플리케이션 관리되는 앱에 대한 요청 본문 예제:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceUri": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

### <a name="responses"></a>응답

코드: 200<br>
OK. 추가 처리 및 청구를 위해 Microsoft 측에서 일괄 처리 사용량 내보내기를 수락하고 기록했습니다. 응답 목록은 일괄 처리의 각 개별 이벤트에 대한 상태와 함께 반환됩니다. 일괄 처리 이벤트의 일부로 전송된 각 개별 사용 이벤트에 대한 응답을 이해하려면 응답 페이로드를 반복해야 합니다.

응답 페이로드 예제: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

`BatchUsageEvent` API 응답에서 참조되는 상태 코드에 대한 설명:

| 상태 코드  | Description |
| ---------- | -------------------- |
| `Accepted` | 수락됨 |
| `Expired` | 만료된 사용 |
| `Duplicate` | 중복된 사용량이 제공되었습니다. |
| `Error` | 오류 코드 |
| `ResourceNotFound` | 제공된 사용량 리소스가 잘못되었습니다. |
| `ResourceNotAuthorized` | 이 리소스에 대한 사용량을 제공할 수 있는 권한이 없습니다. |
| `ResourceNotActive` | 리소스가 일시 중단되었거나 활성화되지 않았습니다. |
| `InvalidDimension` | 사용량이 전달된 차원이 이 제품/계획에 적합하지 않습니다. |
| `InvalidQuantity` | 전달된 수량이 0보다 낮거나 같습니다. |
| `BadArgument` | 입력이 없거나 형식이 잘못되었습니다. |

코드: 400<br>
잘못된 요청입니다. 일괄 처리에 25개가 넘는 사용 이벤트가 포함되었습니다.

코드: 403<br>
사용할 수 없습니다. 권한 부여 토큰이 제공되지 않았거나, 잘못 되었거나, 만료되었습니다.  또는 요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 구독에 액세스하려고 합니다.

## <a name="development-and-testing-best-practices"></a>개발 및 테스트 모범 사례

사용자 지정 요금제 내보내기를 테스트하려면 요금제 API와의 통합을 구현하고, 단위당 가격이 0인 사용자 지정 차원이 정의된 게시 SaaS 제품에 대한 플랜을 만듭니다. 또한 제한된 사용자만 통합에 액세스하고 테스트할 수 있도록 이 제품을 미리 보기로 게시합니다.

제한된 대상 그룹으로 테스트하는 동안 기존 라이브 제품에 대한 비공개 플랜을 사용하여 이 플랜에 대한 액세스를 제한할 수도 있습니다.

## <a name="get-support"></a>지원 받기

게시자 지원 옵션을 파악하고 Microsoft 지원 티켓을 개설하려면 [파트너 센터의 상업용 Marketplace 프로그램 지원](support.md)의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

계량 서비스 API에 대한 자세한 내용은 [Marketplace 계량 서비스 API FAQ](marketplace-metering-service-apis-faq.yml)를 참조하세요.
