---
title: Microsoft 상업적 marketplace의 SaaS 배송 구독 Api v2
description: SaaS 상품 Api 버전 2의 일부인 구독 Api를 사용 하 여 Microsoft AppSource, Azure Marketplace 및 Azure Portal에서 SaaS 제품을 관리 하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ace7aac5a308621ca8032677e7ca7eec3858bdb7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064462"
---
# <a name="saas-fulfillment-subscription-apis-v2-in-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace의 SaaS 배송 구독 Api v2

이 문서에서는 SaaS 처리 구독 Api의 버전 2를 설명 합니다.

## <a name="resolve-a-purchased-subscription"></a>구매한 구독 확인

확인 엔드포인트를 사용하면 게시자는 상업용 Marketplace의 구매 식별 토큰([구매했지만 아직 활성화되지 않음](pc-saas-fulfillment-life-cycle.md#purchased-but-not-yet-activated-pendingfulfillmentstart)에서는 *토큰* 이라고 함)을 영구적으로 구매한 SaaS 구독 ID 및 세부 정보와 교환할 수 있습니다.

고객이 파트너의 방문 페이지 URL로 리디렉션되면 고객 식별 토큰은 이 URL 호출의 *token* 매개 변수로 전달됩니다. 게시자는 이 토큰을 사용하여 확인 요청을 수행해야 합니다. 확인 API 응답에는 SaaS 구독 ID와 구매를 고유하게 식별하는 기타 세부 정보가 포함됩니다. 방문 페이지 URL 호출과 함께 제공된 *토큰* 은 일반적으로 24시간 동안 유효합니다. 받은 *토큰* 이 이미 만료된 경우 최종 사용자에게 다음 지침을 제공하는 것이 좋습니다.

"이 구매를 확인할 수 없습니다. Azure Portal 또는 Microsoft 365 관리 센터에서 이 SaaS 구독을 다시 열고 "계정 구성" 또는 "계정 관리"를 다시 선택하세요."

확인 API를 호출하면 지원되는 상태에 있는 모든 SaaS 구독의 구독 정보 및 상태가 반환됩니다.

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.   |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다. [Azure AD 앱에 따라 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명된 대로 게시자가 토큰 값을 검색하는 경우 `"Bearer <accessaccess_token>"` 형식입니다. |
|  `x-ms-marketplace-token`  | 확인할 구매 식별 *token* 매개 변수입니다.  토큰은 고객이 SaaS 파트너의 웹 사이트(예: `https://contoso.com/signup?token=<token><authorization_token>`)로 리디렉션될 때 방문 페이지 URL 호출을 통해 전달됩니다. <br> <br>  인코딩된 *토큰* 값은 방문 페이지 URL의 일부이므로 이 API 호출에서 매개 변수로 사용하려면 먼저 디코딩해야 합니다.  <br> <br> 다음은 URL `contoso.com/signup?token=ab%2Bcd%2Fef`에서 인코딩된 문자열의 예입니다. 여기서 *token* 은 `ab%2Bcd%2Fef`입니다.  디코딩된 동일한 토큰은 `Ab+cd/ef`입니다. |
| | |

*응답 코드:*

코드: 200 제공된 `x-ms-marketplace-token`에 따라 고유한 SaaS 구독 식별자를 반환합니다.

응답 본문 예제:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

코드: 400 잘못된 요청. `x-ms-marketplace-token`이 없거나, 형식이 잘못되었거나, 유효하지 않거나, 만료되었습니다.

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다.  요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="activate-a-subscription"></a>구독 활성화

SaaS 계정이 최종 사용자에 대해 구성된 후, 게시자는 Microsoft 쪽에서 구독 활성화 API를 호출해야 합니다.  이 API 호출이 성공하기 전에는 고객에게 요금이 청구되지 않습니다.

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.   |
| `subscriptionId` | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 [확인 API](#resolve-a-purchased-subscription)를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다.
 |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 문자열은 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`      |  이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다. [Azure AD 앱에 따라 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명된 대로 게시자가 토큰 값을 검색하는 경우 `"Bearer <access_token>"` 형식입니다. |

*요청 페이로드 예제:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*응답 코드:*

코드: 200 구독이 Microsoft 쪽에서 [구독됨]으로 표시되었습니다.

이 호출에 대한 응답 본문이 없습니다.

코드: 400 잘못된 요청: 유효성을 검사하지 못했습니다.

* `planId`가 요청 페이로드에 없습니다.
* 요청 페이로드의 `planId`가 구매한 제품과 일치하지 않습니다.
* 요청 페이로드의 `quantity`가 구매한 제품과 일치하지 않습니다.
* SaaS 구독이 *구독됨* 또는 *일시 중단됨* 상태입니다.

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다. 요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다.

코드: 404를 찾을 수 없음. SaaS 구독이 *구독 취소됨* 상태입니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="get-list-of-all-subscriptions"></a>모든 구독 목록 가져오기

이 API는 상업용 Marketplace에서 게시자가 게시하는 모든 제품에 대해 구매한 모든 SaaS 구독의 목록을 검색합니다.  가능한 모든 상태의 SaaS 구독이 반환됩니다. 이 정보는 Microsoft 쪽에서 삭제 되지 않으므로 구독 되지 않은 SaaS 등록도 반환 됩니다.

API는 페이지당 100개의 페이지가 매겨진 결과를 반환합니다.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.  |
| `continuationToken`  | 선택적 매개 변수입니다. 결과의 첫 번째 페이지를 검색하려면 빈 상태로 둡니다.  다음 페이지를 검색하려면 `@nextLink` 매개 변수에서 반환된 값을 사용합니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`      |  이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다.  [Azure AD 앱에 따라 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명된 대로 게시자가 토큰 값을 검색하는 경우 `"Bearer <access_token>"` 형식입니다. |

*응답 코드:*

Code: 200 게시자의 권한 부여 토큰에 따라 이 게시자가 만든 모든 제품의 모든 기존 구독 목록을 반환합니다.

*응답 본문 예제:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

이 게시자에 대해 구매한 SaaS 구독을 찾을 수 없는 경우 빈 응답 본문이 반환됩니다.

코드: 403 사용할 수 없음. 권한 부여 토큰을 사용할 수 없거나, 잘못되었거나, 만료되었습니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다. 

코드: 500 내부 서버 오류. API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="get-subscription"></a>구독 가져오기

이 API는 상업용 Marketplace에서 게시자가 게시하는 SaaS 제품에 대해 구매한 특정 SaaS 구독 목록을 검색합니다. 모든 구독 목록을 가져오는 데 사용되는 API를 호출하는 대신 이 호출을 사용하여 특정 SaaS 구독에 대한 모든 정보를 해당 ID로 가져옵니다.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Use 2018-08-31. |
| `subscriptionId`     |  구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 확인 API를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `authorization`     | 이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다. [Azure AD 앱에 따라 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명된 대로 게시자가 토큰 값을 검색하는 경우 `"Bearer <access_token>"` 형식입니다.  |

*응답 코드:*

Code: 200 제공된 `subscriptionId`를 기반으로 SaaS 구독에 대한 세부 정보를 반환합니다.

*응답 본문 예제:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다. 요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다. 

코드: 404를 찾을 수 없음.  지정된 `subscriptionId`를 사용하는 SaaS 구독을 찾을 수 없습니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="list-available-plans"></a>사용 가능한 플랜 나열

이 API는 이 제품의 특정 구매 `subscriptionId`에 의해 식별된 SaaS 제품의 모든 플랜을 검색합니다.  이 호출을 사용하여 SaaS 구독의 수혜자가 구독에 대해 업데이트할 수 있는 모든 프라이빗 및 퍼블릭 플랜 목록을 가져옵니다.  반환된 플랜은 이미 구매한 플랜과 동일한 지역에서 사용할 수 있습니다.

이 호출은 이미 구매한 플랜 외에도 해당 고객에게 제공되는 플랜 목록을 반환합니다.  이 목록은 게시자 사이트의 최종 사용자에게 제공할 수 있습니다.  최종 사용자는 구독 플랜을 반환된 목록의 플랜 중 하나로 변경할 수 있습니다.  목록에 없는 플랜으로 변경하면 실패합니다.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 확인 API를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `x-ms-correlationid`  |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `authorization`     |  이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다.  [Azure AD 앱에 따라 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명된 대로 게시자가 토큰 값을 검색하는 경우 `"Bearer <access_token>"` 형식입니다.  |

*응답 코드:*

Code: 200 이미 구매한 플랜을 포함하여 기존 SaaS 구독에 대해 사용 가능한 모든 플랜 목록을 반환합니다.

응답 본문 예제:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

`subscriptionId`를 찾을 수 없는 경우 빈 응답 본문이 반환됩니다.

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다.  요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 시도하는 것일 수 있습니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다. 

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="change-the-plan-on-the-subscription"></a>구독의 플랜 변경

이 API를 사용하여 SaaS 구독에 대해 구매한 기존 플랜을 새 플랜(퍼블릭 또는 프라이빗)으로 업데이트할 수 있습니다.  상업용 Marketplace에서 구매한 SaaS 구독의 플랜이 게시자 쪽에서 변경될 때 게시자는 이 API를 호출해야 합니다.

이 API는 *활성* 구독에 대해서만 호출할 수 있습니다.  플랜을 다른 기존 플랜(퍼블릭 또는 프라이빗)으로 변경할 수 있지만 그 자체로는 변경할 수 없습니다.  프라이빗 플랜의 경우 파트너 센터에서 고객의 테넌트를 플랜 대상의 일부로 정의해야 합니다.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
| `subscriptionId`     | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 확인 API를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다. |

*요청 헤더:*
 
|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `x-ms-correlationid`  | 클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다.  `"Bearer <access_token>"`Azure AD 앱에 따라 토큰 가져오기[에 설명된 대로 게시자가 토큰 값을 검색하는 경우 ](./pc-saas-registration.md#get-the-token-with-an-http-post) 형식입니다. |

*요청 페이로드 예제:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*응답 코드:*

코드: 202 플랜 변경 요청이 비동기적으로 수락되고 처리되었습니다.  파트너는 **작업 위치 URL** 을 폴링하여 플랜 변경 요청의 성공 또는 실패 여부를 확인해야 합니다.  작업의 최종 상태를 나타내는 *실패*, *성공* 또는 *충돌* 이 수신될 때까지 몇 초마다 폴링을 수행해야 합니다.  최종 작업 상태는 신속하게 반환되지만, 경우에 따라 몇 분 정도 걸릴 수 있습니다.

또한 파트너는 상업용 Marketplace 쪽에서 작업을 완료할 준비가 되면 webhook 알림을 받습니다.  그 후 게시자는 게시자 쪽에서 플랜을 변경합니다.

*응답 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  작업 상태를 가져오는 URL입니다.  예들 들어 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`입니다. |

코드: 400 잘못된 요청: 유효성을 검사할 수 없습니다.

* 이 SaaS 구독에 대한 새 플랜이 없거나 사용할 수 없습니다.
* 새 플랜이 현재 플랜과 동일합니다.
* SaaS 구독 상태가 *구독됨* 이 아닙니다.
* SaaS 구독에 대한 업데이트 작업이 `allowedCustomerOperations`에 포함되지 않았습니다.

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다.  요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다.

코드: 404를 찾을 수 없음.  `subscriptionId`인 SaaS 구독을 찾을 수 없습니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

>[!NOTE]
>플랜 또는 사용자 수량을 한 번에 하나씩 변경할 수 있고, 동시에 변경할 수는 없습니다.

>[!Note]
>이 API는 최종 사용자로부터 명시적인 변경 승인을 받은 후에만 호출할 수 있습니다.

## <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>SaaS 구독의 사용자 수량 변경

이 API를 사용하여 SaaS 구독에 대해 구매한 사용자 수량을 업데이트(증가 또는 감소)합니다.  상업용 Marketplace에서 구매한 SaaS 구독의 사용자 수가 게시자 쪽에서 변경될 때 게시자는 이 API를 호출해야 합니다.

사용자 수량은 현재 플랜에서 허용되는 수량보다 많을 수 없습니다.  이 경우 게시자는 플랜을 변경한 후 사용자 수량을 변경해야 합니다.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 확인 API를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다.  |

*요청 헤더:*
 
|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `x-ms-correlationid`  | 클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     | 이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다.  `"Bearer <access_token>"`Azure AD 앱에 따라 토큰 가져오기[에 설명된 대로 게시자가 토큰 값을 검색하는 경우 ](./pc-saas-registration.md#get-the-token-with-an-http-post) 형식입니다.  |

*요청 페이로드 예제:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*응답 코드:*

코드: 202 수량 변경 요청이 비동기적으로 수락되고 처리되었습니다. 파트너는 **작업 위치 URL** 을 폴링하여 수량 변경 요청의 성공 또는 실패 여부를 확인해야 합니다.  작업의 최종 상태를 나타내는 *실패*, *성공* 또는 *충돌* 이 수신될 때까지 몇 초마다 폴링을 수행해야 합니다.  최종 작업 상태는 신속하게 반환되지만, 경우에 따라 몇 분 정도 걸릴 수 있습니다.

또한 파트너는 상업용 Marketplace 쪽에서 작업을 완료할 준비가 되면 webhook 알림을 받습니다.  그 후 게시자는 게시자 쪽에서 수량을 변경합니다.

*응답 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  작업 상태를 가져오는 리소스의 링크입니다.  예들 들어 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`입니다.  |

코드: 400 잘못된 요청: 유효성을 검사할 수 없습니다.

* 새 수량이 현재 플랜 제한보다 많나 적습니다.
* 새 수량이 없습니다.
* 새 수량이 현재 수량과 동일합니다.
* SaaS 구독 상태가 [구독됨]이 아닙니다.
* SaaS 구독에 대한 업데이트 작업이 `allowedCustomerOperations`에 포함되지 않았습니다.

코드: 403 사용할 수 없음.  권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다.  요청에서 액세스하려는 구독이 현재 게시자에 속하지 않습니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다. 

코드: 404를 찾을 수 없음.  `subscriptionId`인 SaaS 구독을 찾을 수 없습니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

>[!Note]
>플랜 또는 수량을 한 번에 하나씩 변경할 수 있고, 동시에 변경할 수는 없습니다.

>[!Note]
>이 API는 최종 사용자로부터 명시적인 변경 승인을 받은 후에만 호출할 수 있습니다.

## <a name="cancel-a-subscription"></a>구독 취소

이 API를 사용하여 지정된 SaaS 구독을 구독 취소합니다.  게시자는 이 API를 사용할 필요가 없으며 SaaS 구독을 취소하려는 고객을 상업용 Marketplace로 리디렉션하는 것이 좋습니다.

상업용 Marketplace에 구매한 SaaS 구독의 취소를 게시자 쪽에서 구현하기로 결정하는 게시자는 이 API를 호출해야 합니다.  이 호출이 완료된 후에는 Microsoft 쪽에서 구독의 상태가 *구독 취소됨* 으로 변경됩니다.

다음 유예 기간 내에 구독이 취소되면 고객에게 요금이 청구되지 않습니다.

* 월간 구독의 경우 활성화 후 24시간
* 연간 구독의 경우 활성화 후 14일

이전 유예 기간 후에 구독이 취소되면 고객에게 요금이 청구됩니다.  고객은 취소와 동시에 Microsoft 쪽에서 SaaS 구독에 액세스할 수 없게 됩니다. 

### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 확인 API를 사용하여 상업용 Marketplace 권한 부여 토큰을 확인한 후에 가져옵니다.  |

*요청 헤더:*
 
|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `x-ms-correlationid`  | 클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  이 API 호출을 수행하는 게시자를 식별하는 고유한 액세스 토큰입니다.  [Azure AD 앱에 따라 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명된 대로 게시자가 토큰 값을 검색하는 경우 `"Bearer <access_token>"` 형식입니다. |

*응답 코드:*

코드: 202 구독 취소 요청이 비동기적으로 수락되고 처리되었습니다.  파트너는 **작업 위치 URL** 을 폴링하여 이 요청의 성공 또는 실패 여부를 확인해야 합니다.  작업의 최종 상태를 나타내는 *실패*, *성공* 또는 *충돌* 이 수신될 때까지 몇 초마다 폴링을 수행해야 합니다.  최종 작업 상태는 신속하게 반환되지만, 경우에 따라 몇 분 정도 걸릴 수 있습니다.

또한 파트너는 상업용 Marketplace 쪽에서 작업이 성공적으로 완료되면 webhook 알림을 받습니다.  그 후 게시자는 게시자 쪽에서 구독을 취소해야 합니다.

*응답 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  작업 상태를 가져오는 리소스의 링크입니다.  예들 들어 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`입니다. |

코드: 400 잘못된 요청.  이 SaaS 구독의 `allowedCustomerOperations` 목록에 삭제가 없습니다.

코드: 403 사용할 수 없음.  권한 부여 토큰이 잘못되었거나, 만료되었거나, 사용할 수 없습니다. 요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다.

코드: 404를 찾을 수 없음.  `subscriptionId`인 SaaS 구독을 찾을 수 없습니다.

코드: 500 내부 서버 오류. API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="next-steps"></a>다음 단계

상업용 Marketplace의 SaaS 제품에 대한 추가 옵션은 [상업용 Marketplace 서비스 계량 API](../marketplace-metering-service-apis.md)를 참조하세요.

[다른 프로그래밍 언어 및 샘플에 대한 클라이언트](https://github.com/microsoft/commercial-marketplace-samples)를 검토하고 사용해 보세요.
