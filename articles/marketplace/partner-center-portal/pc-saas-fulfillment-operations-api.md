---
title: Microsoft 상업용 Marketplace의 SaaS 처리 작업 API v2
description: SaaS 처리 API 버전 2의 일부인 Operations API를 사용하여 Microsoft AppSource, Azure Marketplace 및 Azure Portal SaaS 제안을 관리하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 11/10/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ef0e735206df6556bd7a7a7b2cf584bc79566a0f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064454"
---
# <a name="saas-fulfillment-operations-apis-v2-in-the-microsoft-commercial-marketplace"></a>Microsoft 상업용 Marketplace의 SaaS 처리 작업 API v2

이 문서에서는 SaaS 처리 작업 API 버전 2에 대해 설명합니다.

## <a name="list-outstanding-operations"></a>미해결 작업 나열

지정된 SaaS 구독에 대해 보류 중인 작업 목록을 가져옵니다. 게시자는 [작업 패치 API](#update-the-status-of-an-operation)를 호출하여 반환된 작업을 승인해야 합니다.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Use 2018-08-31.         |
|    `subscriptionId` | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 확인 API를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다.  |

*요청 헤더:*
 
|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  [Azure AD 앱에 따라 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명된 대로 게시자가 토큰 값을 검색하는 경우 `"Bearer <access_token>"` 형식입니다.  |

*응답 코드:*

코드: 200 지정된 SaaS 구독에서 보류 중인 작업을 반환합니다.

*응답 페이로드 예제:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

보류 중인 작업이 없으면 빈 json을 반환합니다.

코드: 400 잘못된 요청: 유효성을 검사할 수 없습니다.

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다. 요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다.

코드: 404를 찾을 수 없음.  `subscriptionId`인 SaaS 구독을 찾을 수 없습니다.

코드: 500 내부 서버 오류. API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="get-operation-status"></a>작업 상태 가져오기

이 API를 통해 게시자는 지정된 비동기 작업(**Unsubscribe**, **ChangePlan** 또는 **ChangeQuantity**)의 상태를 추적할 수 있습니다.

이 API 호출의 `operationId`는 **작업 위치** 에서 반환된 값, 보류 중인 작업 가져오기 API 호출 또는 webhook 호출에서 받은 `<id>` 매개 변수 값에서 검색할 수 있습니다.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 확인 API를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다. |
|  `operationId`       |  검색 중인 작업의 고유 식별자입니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다.  [Azure AD 앱에 따라 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명된 대로 게시자가 토큰 값을 검색하는 경우 `"Bearer <access_token>"` 형식입니다.  |

*응답 코드:*

코드: 200 지정된 SaaS 작업에 대한 세부 정보를 가져옵니다. 

*응답 페이로드 예제:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다. 요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다.

코드: 404를 찾을 수 없음.  

* `subscriptionId`인 구독을 찾을 수 없습니다.
* `operationId`인 작업을 찾을 수 없습니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="update-the-status-of-an-operation"></a>작업 상태 업데이트

게시자 쪽에서 작업의 성공 또는 실패 여부를 표시하도록 이 API를 사용하여 보류 중인 작업의 상태를 업데이트합니다.

이 API 호출의 `operationId`는 **작업 위치** 에서 반환된 값, 보류 중인 작업 가져오기 API 호출 또는 webhook 호출에서 받은 `<id>` 매개 변수 값에서 검색할 수 있습니다.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Use 2018-08-31.  |
|   `subscriptionId`   |  구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 확인 API를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다.  |
|   `operationId`      |  완료되는 작업의 고유 식별자입니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|   `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `authorization`     |  이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다.  `"Bearer <access_token>"`Azure AD 앱에 따라 토큰 가져오기[에 설명된 대로 게시자가 토큰 값을 검색하는 경우 ](./pc-saas-registration.md#get-the-token-with-an-http-post) 형식입니다. |

*요청 페이로드 예제:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*응답 코드:*

코드: 200 파트너 쪽에서 작업이 완료되었음을 알리기 위한 호출입니다.  예를 들어 이 응답은 게시자 쪽에서 사용자 또는 플랜 변경이 완료되었다는 신호를 보낼 수 있습니다.

코드: 403
- 사용할 수 없습니다.  권한 부여 토큰을 사용할 수 없거나, 잘못되었거나, 만료되었습니다. 요청에서 액세스하려는 구독이 현재 게시자에 속하지 않은 것일 수 있습니다.
- 사용할 수 없습니다.  권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다. 요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다.

코드: 404를 찾을 수 없음.

* `subscriptionId`인 구독을 찾을 수 없습니다.
* `operationId`인 작업을 찾을 수 없습니다.

Code: 409 충돌.  예를 들어 새 업데이트가 이미 수행되었습니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="next-steps"></a>다음 단계

상업용 Marketplace의 SaaS 제품에 대한 추가 옵션은 [상업용 Marketplace 서비스 계량 API](../marketplace-metering-service-apis.md)를 참조하세요.

[다른 프로그래밍 언어 및 샘플에 대한 클라이언트](https://github.com/microsoft/commercial-marketplace-samples)를 검토하고 사용해 보세요.
