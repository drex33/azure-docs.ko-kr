---
title: Microsoft 상업용 Marketplace의 SaaS 처리 API v2
description: 처리 API 버전 2를 사용하여 Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품을 만들고 관리하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: 99735c9e18cb6e278c85254bcebbe514c2fbe0f3
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866562"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>상업용 Marketplace의 SaaS 처리 API 버전 2

이 문서에서는 파트너가 Microsoft AppSource 및 Azure Marketplace에서 SaaS(Software as a Service) 제품을 판매할 수 있도록 하는 API에 대해 자세히 설명합니다. 게시자는 파트너 센터에서 거래 가능 SaaS 제품을 게시하려면 이러한 API와의 통합을 구현해야 합니다.

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS 구독 수명 주기 관리

상업용 Marketplace는 최종 사용자의 SaaS 구독 구매 이후 구독의 전체 수명 주기를 관리합니다. 상업용 Marketplace는 실제 SaaS 구독 활성화, 사용, 업데이트 및 취소를 구동하는 메커니즘으로 방문 페이지, 처리 API, 작업 API 및 webhook를 사용합니다. 최종 사용자의 청구서는 Microsoft에서 유지 관리하는 SaaS 구독의 상태를 기준으로 작성됩니다. 

### <a name="states-of-a-saas-subscription"></a>SaaS 구독의 상태

다음 다이어그램에서는 SaaS 구독의 상태와 해당하는 작업을 보여줍니다.

![마켓플레이스의 Software as a Service 구독 수명 주기를 보여주는 다이어그램](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>구매했지만 아직 활성화되지 않음(*PendingFulfillmentStart*)

최종 사용자 또는 CSP (클라우드 솔루션 공급자)가 상업적 marketplace에서 SaaS 제품을 구매한 후에는 게시자에 게 구매 알림이 제공 됩니다. 그러면 게시자는 게시자 쪽에 최종 사용자를 위한 새 SaaS 계정을 만들고 구성할 수 있습니다.

계정이 만들어지는 과정은 다음과 같습니다.

1. 고객은 Microsoft AppSource 또는 Azure Portal에서 성공적으로 구매한 후 SaaS 제품에 사용할 수 있는 **지금 계정 구성** 단추를 선택 합니다. 또는 고객이 구매 후 즉시 받게 될 전자 메일에서 **지금 구성** 단추를 사용할 수 있습니다.
2. 그러면 Microsoft는 새 브라우저 탭에서 토큰 매개 변수(상업용 Marketplace의 구매 식별 토큰)가 포함된 방문 페이지 URL을 열어서 파트너에게 구매 소식을 알립니다.

이러한 호출의 예로 `https://contoso.com/signup?token=<blob>`이 있으며, 파트너 센터에서는 이 SaaS 제품의 방문 페이지 URL이 `https://contoso.com/signup`으로 구성됩니다. 이 토큰은 SaaS 구매 및 고객을 고유하게 식별하는 ID를 게시자에게 제공합니다.

[!INCLUDE [pound-sign-note](../includes/pound-sign-note.md)]

> [!IMPORTANT]
> 방문 페이지 URL은 항상 가동해야 하며 언제나 Microsoft에서 새 호출을 받을 준비가 되어 있어야 합니다. 방문 페이지를 사용할 수 없게 되면 고객이 SaaS 서비스에 가입하고 서비스 사용을 시작할 수 없습니다.

다음으로, 게시자는 [SaaS 확인 API](#resolve-a-purchased-subscription)를 호출하고 `x-ms-marketplace-token header` 헤더 매개 변수의 값으로 토큰을 입력하여 *토큰* 을 Microsoft에 다시 전달해야 합니다. 확인 API를 호출한 결과로 고유 구매 ID, 구매한 제품 ID, 구매한 플랜 ID와 같은 SaaS 구매 세부 정보와 토큰이 교환됩니다.

방문 페이지에서 고객은 Azure AD(Azure Active Directory) SSO(Single Sign-On)를 통해 신규 또는 기존 SaaS 계정에 로그인해야 합니다.

>[!NOTE]
>고객이 Microsoft 쪽에서 구성 프로세스를 시작하기 전에는 게시자에게 SaaS 구매 알림이 전달되지 않습니다.

게시자는 이 흐름에 대해 Microsoft에서 필수로 요구하는 사용자 환경을 제공할 수 있도록 SSO를 구현해야 합니다. SSO를 구성할 때 다중 테넌트 Azure AD 애플리케이션을 사용해야 하며 회사 및 학교 계정 또는 개인 Microsoft 계정을 모두 허용해야 합니다. 이 요구 사항은 Microsoft 자격 증명을 사용하여 이미 로그인한 경우 SaaS 서비스로 리디렉션되는 사용자의 방문 페이지에만 적용됩니다. SSO는 SaaS 서비스에 대한 일부 로그인에만 필요합니다.

> [!NOTE]
>SSO를 사용하려면 관리자가 앱에 권한을 부여해야 하는 경우 파트너 센터의 제품 설명에 관리자 수준 액세스가 필요하다는 것을 공개해야 합니다. 이러한 공개는 [상업용 Marketplace 인증 정책](/legal/marketplace/certification-policies#10003-authentication-options)을 준수하기 위한 것입니다.

로그인 후 고객은 게시자 쪽에서 SaaS 구성을 완료해야 합니다. 그 후 게시자는 [구독 활성화 API](#activate-a-subscription)를 호출하여 SaaS 계정 프로비전이 완료되었다는 신호를 Azure Marketplace에 보내야 합니다.
이 작업은 고객의 청구 주기를 시작합니다. 구독 활성화 API 호출이 실패하면 고객에게 구매 대금이 청구되지 않습니다.


![프로비전 시나리오에 대한 API 호출을 보여주는 다이어그램](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>활성(*구독됨*)

*활성(구독됨)* 은 프로비전된 SaaS 구독의 안정적인 상태입니다. Microsoft 쪽에서 [구독 활성화 API](#activate-a-subscription) 호출을 처리한 후에는 SaaS 구독이 *구독됨* 으로 표시됩니다. 이제 고객이 게시자 쪽에서 SaaS 서비스를 사용할 수 있으며 요금이 청구됩니다.

SaaS 구독이 이미 활성 상태인 경우 고객은 Azure Portal 또는 Microsoft 365 관리 센터에서 **SaaS 환경 관리** 를 선택할 수 있습니다. 이 작업을 수행하면 활성화 흐름과 마찬가지로Microsoft가 *token* 매개 변수를 사용하여 **방문 페이지 URL** 을 호출하게 됩니다. 게시자는 신규 구매와 기존 SaaS 계정의 관리를 구분하고 그에 따라 이 방문 페이지 URL 호출을 처리해야 합니다.

#### <a name="being-updated-subscribed"></a>업데이트 중(*구독됨*)

이 작업은 Microsoft와 게시자 모두 기존 활성 SaaS 구독의 업데이트를 처리하고 있다는 뜻입니다. 이러한 업데이트는 다음 주체가 시작할 수 있습니다.

- 상업용 Marketplace의 고객
- 상업용 Marketplace의 CSP
- 게시자의 SaaS 사이트 고객(CSP가 구매한 제품의 고객은 아님)

SaaS 구독은 다음과 같은 두 가지 유형의 업데이트를 사용할 수 있습니다.

- 고객이 구독의 다른 플랜을 선택할 때 플랜을 업데이트합니다.
- 고객이 구독에 대해 구매한 사용자 수를 변경할 때 수량을 업데이트합니다.

활성 구독만 업데이트할 수 있습니다. 구독이 업데이트되는 동안 해당 상태는 Microsoft 쪽에서 활성 상태로 유지됩니다.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>상업용 Marketplace에서 시작된 업데이트

이 흐름에서 고객은 Azure Portal 또는 Microsoft 365 관리 센터에서 구독 플랜 또는 사용자 수량을 변경합니다.

1. 업데이트를 입력 한 후 Microsoft는 파트너 센터의 _기술 구성_ 페이지에 있는 **연결 webhook** 필드에 구성 된 게시자의 웹 후크 URL을 호출 하 고 적절 한 *작업* 및 기타 관련 매개 변수 값을 호출 합니다.
1. 게시자 쪽에서는 SaaS 서비스를 필요한 대로 변경하고, 모두 마쳤으면 [작업 상태 업데이트 API](#update-the-status-of-an-operation)를 호출하여 Microsoft에 알려야 합니다.
1. 패치가 *실패* 상태와 함께 전송되는 경우 Microsoft 쪽에서 업데이트 프로세스가 완료되지 않습니다. SaaS 구독의 기존 플랜과 사용자 수량이 유지됩니다.

> [!NOTE]
> 게시자는 webhook 알림을 받은 후 *10초 내에* PATCH를 호출하여 [작업 상태 API](#update-the-status-of-an-operation)를 실패/성공 응답으로 업데이트해야 합니다. 작업 상태 PATCH가 10초 내에 수신되지 않으면 플랜 변경이 *자동으로 성공으로 패치* 됩니다. 

다음 다이어그램에는 상업용 Marketplace에서 시작되는 업데이트 시나리오의 API 호출 시퀀스가 나와 있습니다.

![마켓플레이스에서 시작되는 업데이트에 대한 API 호출을 보여주는 다이어그램](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>게시자 쪽에서 시작되는 업데이트

이 흐름에서 고객은 SaaS 서비스 자체에서 구매한 구독 플랜 또는 사용자 수량을 변경합니다. 

1. 게시자 쪽에서 변경 요청을 수행하려면 먼저 게시자 코드에서 [플랜 변경 API](#change-the-plan-on-the-subscription) 및/또는 [수량 변경 API](#change-the-quantity-of-seats-on-the-saas-subscription)를 호출해야 합니다. 

1. Microsoft에서 구독에 변경 내용을 적용 한 다음 동일한 변경 내용을 적용 하도록 **연결 webhook** 를 통해 게시자에 게 알립니다.

1. 그 후 게시자는 SaaS 구독을 필요한 대로 변경하고, 변경을 마친 후 [작업 상태 업데이트 API](#update-the-status-of-an-operation)를 호출하여 Microsoft에 알려야 합니다.

다음 다이어그램에는 게시자 쪽에서 시작되는 업데이트 시나리오의 API 호출 시퀀스가 나와 있습니다.

![게시자 쪽에서 시작되는 업데이트에 대한 API 호출을 보여주는 다이어그램](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>일시 중단됨(*일시 중단됨*)

이 상태는 고객의 SaaS 서비스 요금 결제가 이루어지지 않았다는 것을 나타냅니다. Microsoft에서 제공하는 SaaS 구독 상태를 통해 이 변경 내용이 게시자에게 전달됩니다. 알림은 *action* 매개 변수를 *Suspended* 로 설정한 webhook 호출을 통해 수행됩니다.

게시자가 게시자 쪽에서 SaaS 서비스를 변경할 수 없습니다. 게시자는 이 정보를 일시 중단된 고객에게 제공하고 고객의 SaaS 서비스 액세스를 제한하거나 차단하는 것이 좋습니다. 요금 결제를 전혀 받지 못할 가능성이 있습니다.

> [!NOTE]
> Microsoft는 구독을 자동으로 취소하기 전에 고객에게 30일의 유예 기간을 줍니다. 30 일 유예 기간이 지나면 웹 후크는 [구독 취소](#cancel-a-subscription) 작업을 받게 됩니다.

구독이 *일시 중단됨* 상태인 경우:

* 파트너 또는 ISV는 데이터 또는 설정 손실 없이 전체 기능을 복원할 수 있도록 SaaS 계정을 복구 가능한 상태로 유지해야 합니다.
* 파트너 또는 ISV는 유예 기간 동안 결제가 이루어지면 구독 복구 요청이, 유예 기간이 종료되면 구독 프로비전 해제 요청이 있을 것으로 예상해야 합니다. 두 요청은 모두 webhook 메커니즘을 통해 전송됩니다.

게시자가 어떤 조치를 취하기 전에는 구독 상태가 Microsoft 쪽에서 [일시 중단됨]으로 변경됩니다. 활성 구독만 일시 중단할 수 있습니다.

#### <a name="reinstated-suspended"></a>복구됨(*일시 중단됨*)

이 작업은 고객의 결제 방법을 다시 사용할 수 있고, SaaS 구독 요금이 결제되었으며, 구독을 복구하는 중임을 나타냅니다. 이 경우 다음과 같습니다. 

1. Microsoft는 *action* 매개 변수가 *Reinstate* 값으로 설정된 webhook를 호출합니다.
1. 게시자는 구독이 게시자 쪽에서 다시 완전히 작동하는지 확인합니다.
1. 게시자가 성공 상태를 사용하여 [패치 작업 API](#update-the-status-of-an-operation)를 호출합니다.
1. 복구 프로세스가 성공하고 고객에게 SaaS 구독 요금이 다시 청구됩니다. 

패치가 *실패* 상태와 함께 전송되면 복구 프로세스가 Microsoft 쪽에서 완료되지 않고 구독은 *일시 중단됨* 상태로 유지됩니다.

일시 중단된 구독만 복구할 수 있습니다. 일시 중단된 SaaS 구독은 복구되는 동안 *일시 중단됨* 상태로 유지됩니다. 이 작업이 완료되면 구독의 상태가 *활성* 으로 변경됩니다.

#### <a name="renewed-subscribed"></a>갱신됨(*구독됨*)

SaaS 구독은 월간 또는 연간 구독 기간이 끝날 때 Microsoft에서 자동으로 갱신합니다. 모든 SaaS 구독의 자동 갱신 설정의 기본값은 *true* 입니다. 활성 SaaS 구독은 정기적으로 계속 갱신됩니다. Microsoft는 갱신 이벤트에 대 한 알림 전용 webhook 알림을 제공 합니다. 고객은 Microsoft 365 관리 포털을 통해 SaaS 구독의 자동 갱신을 해제할 수 있습니다. 이 경우 SaaS 구독은 현재 청구 기간 종료 시 자동으로 취소됩니다. 고객은 언제든지 SaaS 구독을 취소할 수 있습니다.

활성 구독만 자동으로 갱신됩니다. 갱신 프로세스가 진행되는 동안 그리고 자동 갱신이 성공하면 구독이 활성 상태를 유지합니다. 갱신 후 구독 기간의 시작 및 종료 날짜가 새 기간의 날짜로 업데이트됩니다.

결제 문제로 인해 자동 갱신이 실패하면 구독이 *일시 중단* 되고 게시자에게 알림이 전달됩니다.

#### <a name="canceled-unsubscribed"></a>취소됨(*구독 취소됨*)

게시자 사이트, Azure Portal 또는 Microsoft 365 관리 센터에서 구독을 취소하면 명시적 고객 또는 CSP 작업에 대한 응답으로 구독이 이 상태에 도달합니다. 또한 *일시 중단됨* 상태로 전환된 후 30일이 지나도 요금 결제가 이루어지지 않으면 구독이 암시적으로 취소될 수 있습니다.

게시자는 취소 webhook 호출을 받은 후 최소 7일 동안 요청 시 복구할 수 있도록 고객 데이터를 보존해야 합니다. 이 기간이 지나야만 고객 데이터를 삭제할 수 있습니다.

SaaS 구독은 수명 주기 동안 언제든지 취소할 수 있습니다. 구독을 취소한 후에는 다시 활성화할 수 없습니다.

## <a name="api-reference"></a>API 참조

이 섹션에서는 SaaS 구독 및 작업 API에 대해 설명합니다.

**구독 API** 는 구매부터 취소까지 SaaS 구독 수명 주기를 처리하는 데 사용해야 합니다.

**작업 API** 는 다음을 수행하는 데 사용해야 합니다.

* 처리된 webhook 호출을 확인하고 승인합니다.
* 게시자의 승인을 기다리는 앱의 보류 중인 작업 목록을 가져옵니다.

> [!NOTE]
> TLS 1.2 버전은 HTTPS 통신을 위한 최소 버전으로 곧 적용됩니다. 코드에서 이 TLS 버전을 사용해야 합니다. TLS 1.0 및 1.1 버전은 곧 사용이 중단될 예정입니다.

### <a name="subscription-apis"></a>구독 API

#### <a name="resolve-a-purchased-subscription"></a>구매한 구독 확인

확인 엔드포인트를 사용하면 게시자는 상업용 Marketplace의 구매 식별 토큰([구매했지만 아직 활성화되지 않음](#purchased-but-not-yet-activated-pendingfulfillmentstart)에서는 *토큰* 이라고 함)을 영구적으로 구매한 SaaS 구독 ID 및 세부 정보와 교환할 수 있습니다.

고객이 파트너의 방문 페이지 URL로 리디렉션되면 고객 식별 토큰은 이 URL 호출의 *token* 매개 변수로 전달됩니다. 게시자는 이 토큰을 사용하여 확인 요청을 수행해야 합니다. 확인 API 응답에는 SaaS 구독 ID와 구매를 고유하게 식별하는 기타 세부 정보가 포함됩니다. 방문 페이지 URL 호출과 함께 제공된 *토큰* 은 일반적으로 24시간 동안 유효합니다. 받은 *토큰* 이 이미 만료된 경우 최종 사용자에게 다음 지침을 제공하는 것이 좋습니다.

"이 구매를 확인할 수 없습니다. Azure Portal 또는 Microsoft 365 관리 센터에서 이 SaaS 구독을 다시 열고 "계정 구성" 또는 "계정 관리"를 다시 선택하세요."

확인 API를 호출하면 지원되는 상태에 있는 모든 SaaS 구독의 구독 정보 및 상태가 반환됩니다.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

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

#### <a name="activate-a-subscription"></a>구독 활성화

SaaS 계정이 최종 사용자에 대해 구성된 후, 게시자는 Microsoft 쪽에서 구독 활성화 API를 호출해야 합니다.  이 API 호출이 성공하기 전에는 고객에게 요금이 청구되지 않습니다.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

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

#### <a name="get-list-of-all-subscriptions"></a>모든 구독 목록 가져오기

이 API는 상업용 Marketplace에서 게시자가 게시하는 모든 제품에 대해 구매한 모든 SaaS 구독의 목록을 검색합니다.  가능한 모든 상태의 SaaS 구독이 반환됩니다. 이 정보는 Microsoft 쪽에서 삭제되지 않으므로 구독 취소된 SaaS 구독도 반환됩니다.

API는 페이지당 100개의 페이지가 매겨진 결과를 반환합니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

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

#### <a name="get-subscription"></a>구독 가져오기

이 API는 상업용 Marketplace에서 게시자가 게시하는 SaaS 제품에 대해 구매한 특정 SaaS 구독 목록을 검색합니다. 모든 구독 목록을 가져오는 데 사용되는 API를 호출하는 대신 이 호출을 사용하여 특정 SaaS 구독에 대한 모든 정보를 해당 ID로 가져옵니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

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

#### <a name="list-available-plans"></a>사용 가능한 플랜 나열

이 API는 이 제품의 특정 구매 `subscriptionId`에 의해 식별된 SaaS 제품의 모든 플랜을 검색합니다.  이 호출을 사용하여 SaaS 구독의 수혜자가 구독에 대해 업데이트할 수 있는 모든 프라이빗 및 퍼블릭 플랜 목록을 가져옵니다.  반환된 플랜은 이미 구매한 플랜과 동일한 지역에서 사용할 수 있습니다.

이 호출은 이미 구매한 플랜 외에도 해당 고객에게 제공되는 플랜 목록을 반환합니다.  이 목록은 게시자 사이트의 최종 사용자에게 제공할 수 있습니다.  최종 사용자는 구독 플랜을 반환된 목록의 플랜 중 하나로 변경할 수 있습니다.  목록에 없는 플랜으로 변경하면 실패합니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

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

#### <a name="change-the-plan-on-the-subscription"></a>구독의 플랜 변경

이 API를 사용하여 SaaS 구독에 대해 구매한 기존 플랜을 새 플랜(퍼블릭 또는 프라이빗)으로 업데이트할 수 있습니다.  상업용 Marketplace에서 구매한 SaaS 구독의 플랜이 게시자 쪽에서 변경될 때 게시자는 이 API를 호출해야 합니다.

이 API는 *활성* 구독에 대해서만 호출할 수 있습니다.  플랜을 다른 기존 플랜(퍼블릭 또는 프라이빗)으로 변경할 수 있지만 그 자체로는 변경할 수 없습니다.  프라이빗 플랜의 경우 파트너 센터에서 고객의 테넌트를 플랜 대상의 일부로 정의해야 합니다.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

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

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>SaaS 구독의 사용자 수량 변경

이 API를 사용하여 SaaS 구독에 대해 구매한 사용자 수량을 업데이트(증가 또는 감소)합니다.  상업용 Marketplace에서 구매한 SaaS 구독의 사용자 수가 게시자 쪽에서 변경될 때 게시자는 이 API를 호출해야 합니다.

사용자 수량은 현재 플랜에서 허용되는 수량보다 많을 수 없습니다.  이 경우 게시자는 플랜을 변경한 후 사용자 수량을 변경해야 합니다.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

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

#### <a name="cancel-a-subscription"></a>구독 취소

이 API를 사용하여 지정된 SaaS 구독을 구독 취소합니다.  게시자는 이 API를 사용할 필요가 없으며 SaaS 구독을 취소하려는 고객을 상업용 Marketplace로 리디렉션하는 것이 좋습니다.

상업용 Marketplace에 구매한 SaaS 구독의 취소를 게시자 쪽에서 구현하기로 결정하는 게시자는 이 API를 호출해야 합니다.  이 호출이 완료된 후에는 Microsoft 쪽에서 구독의 상태가 *구독 취소됨* 으로 변경됩니다.

다음 유예 기간 내에 구독이 취소되면 고객에게 요금이 청구되지 않습니다.

* 월간 구독의 경우 활성화 후 24시간
* 연간 구독의 경우 활성화 후 14일

이전 유예 기간 후에 구독이 취소되면 고객에게 요금이 청구됩니다.  고객은 취소와 동시에 Microsoft 쪽에서 SaaS 구독에 액세스할 수 없게 됩니다. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

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

### <a name="operations-apis"></a>작업 API

#### <a name="list-outstanding-operations"></a>미해결 작업 나열

지정된 SaaS 구독에 대해 보류 중인 작업 목록을 가져옵니다.  게시자는 [작업 패치 API](#update-the-status-of-an-operation)를 호출하여 반환된 작업을 승인해야 합니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

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

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다.  요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다. 

코드: 404를 찾을 수 없음.  `subscriptionId`인 SaaS 구독을 찾을 수 없습니다.

코드: 500 내부 서버 오류. API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

#### <a name="get-operation-status"></a>작업 상태 가져오기

이 API를 통해 게시자는 지정된 비동기 작업(**Unsubscribe**, **ChangePlan** 또는 **ChangeQuantity**)의 상태를 추적할 수 있습니다.

이 API 호출의 `operationId`는 **작업 위치** 에서 반환된 값, 보류 중인 작업 가져오기 API 호출 또는 webhook 호출에서 받은 `<id>` 매개 변수 값에서 검색할 수 있습니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

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

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다.  요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다. 

코드: 404를 찾을 수 없음.  

* `subscriptionId`인 구독을 찾을 수 없습니다.
* `operationId`인 작업을 찾을 수 없습니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

#### <a name="update-the-status-of-an-operation"></a>작업 상태 업데이트

게시자 쪽에서 작업의 성공 또는 실패 여부를 표시하도록 이 API를 사용하여 보류 중인 작업의 상태를 업데이트합니다.

이 API 호출의 `operationId`는 **작업 위치** 에서 반환된 값, 보류 중인 작업 가져오기 API 호출 또는 webhook 호출에서 받은 `<id>` 매개 변수 값에서 검색할 수 있습니다.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

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
- 사용할 수 없습니다.  권한 부여 토큰이 잘못되었거나, 만료되었거나, 제공되지 않았습니다.  요청이 권한 부여 토큰을 만드는 데 사용된 것과 다른 Azure AD 앱 ID를 사용하여 게시된 제품의 SaaS 구독에 액세스하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md)을 제대로 수행하지 않았을 때 발생하는 증상입니다.

코드: 404를 찾을 수 없음.

* `subscriptionId`인 구독을 찾을 수 없습니다.
* `operationId`인 작업을 찾을 수 없습니다.

Code: 409 충돌.  예를 들어 새 업데이트가 이미 수행되었습니다.

코드: 500 내부 서버 오류.  API 호출을 다시 시도하세요.  오류가 지속되면 [Microsoft 지원](https://go.microsoft.com/fwlink/?linkid=2165533)에 문의하세요.

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS 서비스에서 webhook 구현

파트너 센터 거래 가능한 SaaS 제안을 만들 때 파트너는 HTTP 엔드포인트로 사용할 **연결 웹후크** URL을 제공합니다.  이 webhook는 Microsoft가 Microsoft 쪽에서 발생하는 다음 이벤트를 게시자 쪽에 알리기 위해 POST HTTP 호출을 사용하여 호출합니다.

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
> webhook URL 서비스는 연중 무휴 실행되어야 하며, 항상 Microsoft에서 새로운 호출을 받을 준비가 되어 있어야 합니다.  Microsoft에는 webhook 호출에 대한 재시도 정책이 있지만(8시간 동안 500회 재시도) 게시자가 통화를 수락하고 응답을 반환하지 않으면 결국 Microsoft 쪽에서 webhook가 알리게 되는 작업이 실패합니다.

*구매 이벤트의 Webhook 페이로드 예제:*

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

*구독 복구 이벤트의 Webhook 페이로드 예제:*

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

*갱신 이벤트의 Webhook 페이로드 예제:*

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
