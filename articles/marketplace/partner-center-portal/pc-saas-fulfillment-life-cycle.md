---
title: SaaS 구독 수명 주기 관리 | Microsoft 상업용 Marketplace
description: 처리 API 버전 2를 사용하여 SaaS 구독 수명 주기를 관리하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: dfe47c220cb0dc427d9ff00bdfbaa85da949f520
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132064447"
---
# <a name="managing-the-saas-subscription-life-cycle"></a>SaaS 구독 수명 주기 관리

상업용 Marketplace는 최종 사용자의 SaaS 구독 구매 이후 구독의 전체 수명 주기를 관리합니다. 상업용 Marketplace는 실제 SaaS 구독 활성화, 사용, 업데이트 및 취소를 구동하는 메커니즘으로 방문 페이지, 처리 API, 작업 API 및 webhook를 사용합니다. 최종 사용자의 청구서는 Microsoft에서 유지 관리하는 SaaS 구독의 상태를 기준으로 작성됩니다.

## <a name="states-of-a-saas-subscription"></a>SaaS 구독의 상태

다음 다이어그램에서는 SaaS 구독의 상태와 해당하는 작업을 보여줍니다.

[![마켓플레이스에서 Software as a Service 구독의 수명 주기를 보여 주는 다이어그램 ](./media/saas-subscription-lifecycle-api-v2.png) ](./media/saas-subscription-lifecycle-api-v2.png#lightbox)

### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>구매했지만 아직 활성화되지 않음(*PendingFulfillmentStart*)

최종 사용자 또는 CSP(클라우드 솔루션 공급자)가 상업용 Marketplace에서 SaaS 제안을 구매하면 게시자에게 구매 알림이 표시됩니다. 그러면 게시자는 게시자 쪽에 최종 사용자를 위한 새 SaaS 계정을 만들고 구성할 수 있습니다.

계정이 만들어지는 과정은 다음과 같습니다.

1. 고객은 Microsoft AppSource 또는 Azure Portal 성공적으로 구매한 후 SaaS 제안에 사용할 수 있는 **지금 계정 구성** 단추를 선택합니다. 또는 고객이 구매 직후 받을 전자 메일의 **지금 구성** 단추를 사용할 수 있습니다.
2. 그런 다음 Microsoft는 새 브라우저 탭에서 토큰 매개 변수(상업용 Marketplace의 구매 ID 토큰)를 사용하여 방문 페이지 URL을 열어 파트너에게 구매에 대해 알 수 있습니다.

이러한 호출의 예는 `https://contoso.com/signup?token=<blob>` 이지만 파트너 센터 이 SaaS 제안에 대한 방문 페이지 URL은 로 구성됩니다. `https://contoso.com/signup` 이 토큰은 SaaS 구매 및 고객을 고유하게 식별하는 ID를 게시자에게 제공합니다.

[!INCLUDE [pound-sign-note](../includes/pound-sign-note.md)]

> [!IMPORTANT]
> 방문 페이지 URL은 항상 가동해야 하며 언제나 Microsoft에서 새 호출을 받을 준비가 되어 있어야 합니다. 방문 페이지를 사용할 수 없게 되면 고객이 SaaS 서비스에 가입하고 서비스 사용을 시작할 수 없습니다.

다음으로, 게시자는 [SaaS 확인 API](pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription)를 호출하고 `x-ms-marketplace-token header` 헤더 매개 변수의 값으로 토큰을 입력하여 *토큰* 을 Microsoft에 다시 전달해야 합니다. Resolve API 호출의 결과로 토큰은 구매의 고유 ID, 구매한 제안 ID 및 구매한 계획 ID와 같은 SaaS 구매에 대한 세부 정보를 교환합니다.

방문 페이지에서 고객은 Azure AD(Azure Active Directory) SSO(Single Sign-On)를 통해 신규 또는 기존 SaaS 계정에 로그인해야 합니다.

>[!NOTE]
>고객이 Microsoft 쪽에서 구성 프로세스를 시작하기 전에는 게시자에게 SaaS 구매 알림이 전달되지 않습니다.

게시자는 이 흐름에 대해 Microsoft에서 필수로 요구하는 사용자 환경을 제공할 수 있도록 SSO를 구현해야 합니다. SSO를 구성할 때 다중 테넌트 Azure AD 애플리케이션을 사용해야 하며 회사 및 학교 계정 또는 개인 Microsoft 계정을 모두 허용해야 합니다. 이 요구 사항은 Microsoft 자격 증명을 사용하여 이미 로그인한 경우 SaaS 서비스로 리디렉션되는 사용자의 방문 페이지에만 적용됩니다. SSO는 SaaS 서비스에 대한 일부 로그인에만 필요합니다.

> [!NOTE]
>SSO를 사용하려면 관리자가 앱에 권한을 부여해야 하는 경우 파트너 센터의 제품 설명에 관리자 수준 액세스가 필요하다는 것을 공개해야 합니다. 이러한 공개는 [상업용 Marketplace 인증 정책](/legal/marketplace/certification-policies#10003-authentication-options)을 준수하기 위한 것입니다.

로그인한 후 고객은 게시자 쪽에서 SaaS 구성을 완료해야 합니다. 그 후 게시자는 [구독 활성화 API](pc-saas-fulfillment-subscription-api.md#activate-a-subscription)를 호출하여 SaaS 계정 프로비전이 완료되었다는 신호를 Azure Marketplace에 보내야 합니다.
이 작업은 고객의 청구 주기를 시작합니다. 구독 활성화 API 호출이 실패하면 고객에게 구매 대금이 청구되지 않습니다.

![프로비전 시나리오에 대한 API 호출을 보여주는 다이어그램](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

### <a name="active-subscribed"></a>활성(*구독됨*)

*활성(구독됨)* 은 프로비전된 SaaS 구독의 안정적인 상태입니다. Microsoft 쪽에서 [구독 활성화 API](pc-saas-fulfillment-subscription-api.md#activate-a-subscription) 호출을 처리한 후에는 SaaS 구독이 *구독됨* 으로 표시됩니다. 이제 고객이 게시자 쪽에서 SaaS 서비스를 사용할 수 있으며 요금이 청구됩니다.

SaaS 구독이 이미 활성화된 경우 고객은 Azure Portal 또는 Microsoft 365 관리 센터에서 **SaaS 환경 관리를** 선택할 수 있습니다. 이 작업을 수행하면 활성화 흐름과 마찬가지로Microsoft가 *token* 매개 변수를 사용하여 **방문 페이지 URL** 을 호출하게 됩니다. 게시자는 신규 구매와 기존 SaaS 계정의 관리를 구분하고 그에 따라 이 방문 페이지 URL 호출을 처리해야 합니다.

### <a name="being-updated-subscribed"></a>업데이트 중(*구독됨*)

이 작업은 Microsoft와 게시자 모두 기존 활성 SaaS 구독의 업데이트를 처리하고 있다는 뜻입니다. 이러한 업데이트는 다음 주체가 시작할 수 있습니다.

- 상업용 Marketplace의 고객
- 상업용 Marketplace의 CSP
- 게시자의 SaaS 사이트 고객(CSP가 구매한 제품의 고객은 아님)

SaaS 구독은 다음과 같은 두 가지 유형의 업데이트를 사용할 수 있습니다.

- 고객이 구독의 다른 플랜을 선택할 때 플랜을 업데이트합니다.
- 고객이 구독에 대해 구매한 사용자 수를 변경할 때 수량을 업데이트합니다.

활성 구독만 업데이트할 수 있습니다. 구독이 업데이트되는 동안 해당 상태는 Microsoft 쪽에서 활성 상태로 유지됩니다.

#### <a name="update-initiated-from-the-commercial-marketplace"></a>상업용 Marketplace에서 시작된 업데이트

이 흐름에서 고객은 Azure Portal 또는 Microsoft 365 관리 센터에서 구독 플랜 또는 사용자 수량을 변경합니다.

1. 업데이트가 입력되면 Microsoft는 *작업* 및 기타 관련 매개 변수에 적절한 값을 사용하여 파트너 센터 _기술 구성_ 페이지의 연결 웹후크 필드에 구성된 게시자의 **웹후크** URL을 호출합니다.
1. 게시자 쪽에서는 SaaS 서비스를 필요한 대로 변경하고, 모두 마쳤으면 [작업 상태 업데이트 API](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation)를 호출하여 Microsoft에 알려야 합니다.
1. 패치가 *실패* 상태와 함께 전송되는 경우 Microsoft 쪽에서 업데이트 프로세스가 완료되지 않습니다. SaaS 구독의 기존 플랜과 사용자 수량이 유지됩니다.

> [!NOTE]
> 게시자는 webhook 알림을 받은 후 *10초 내에* PATCH를 호출하여 [작업 상태 API](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation)를 실패/성공 응답으로 업데이트해야 합니다. 작업 상태 PATCH가 10초 내에 수신되지 않으면 플랜 변경이 *자동으로 성공으로 패치* 됩니다.

다음 다이어그램에는 상업용 Marketplace에서 시작되는 업데이트 시나리오의 API 호출 시퀀스가 나와 있습니다.

![마켓플레이스에서 시작되는 업데이트에 대한 API 호출을 보여주는 다이어그램](./media/saas-update-status-api-v2-calls-marketplace-side.png)

#### <a name="update-initiated-from-the-publisher"></a>게시자 쪽에서 시작되는 업데이트

이 흐름에서 고객은 SaaS 서비스 자체에서 구매한 구독 플랜 또는 사용자 수량을 변경합니다. 

1. 게시자 쪽에서 요청된 변경을 하기 전에 게시자 코드는 [변경 계획 API](pc-saas-fulfillment-subscription-api.md#change-the-plan-on-the-subscription) 또는 변경 [수량 API](pc-saas-fulfillment-subscription-api.md#change-the-quantity-of-seats-on-the-saas-subscription) 또는 둘 다 호출해야 합니다.

1. Microsoft는 구독에 변경 사항을 적용한 다음 **연결 웹후크를** 통해 게시자에게 동일한 변경 사항을 적용하도록 알립니다.

1. 그 후 게시자는 SaaS 구독을 필요한 대로 변경하고, 변경을 마친 후 [작업 상태 업데이트 API](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation)를 호출하여 Microsoft에 알려야 합니다.

다음 다이어그램에는 게시자 쪽에서 시작되는 업데이트 시나리오의 API 호출 시퀀스가 나와 있습니다.

![게시자 쪽에서 시작되는 업데이트에 대한 API 호출을 보여주는 다이어그램](./media/saas-update-status-api-v2-calls-publisher-side.png)

### <a name="suspended-suspended"></a>일시 중단됨(*일시 중단됨*)

이 상태는 SaaS 서비스에 대한 고객의 지불이 수신되지 않음을 나타냅니다. Microsoft는 게시자에게 SaaS 구독 상태의 이 변경 사항을 알립니다. 알림은 *action* 매개 변수를 *Suspended* 로 설정한 webhook 호출을 통해 수행됩니다.

게시자가 게시자 쪽에서 SaaS 서비스를 변경할 수 없습니다. 게시자는 이 정보를 일시 중단된 고객에게 제공하고 고객의 SaaS 서비스 액세스를 제한하거나 차단하는 것이 좋습니다. 결제가 절대 수신되지 않을 가능성이 있습니다.

> [!NOTE]
> Microsoft는 구독을 자동으로 취소하기 전에 고객에게 30일의 유예 기간을 줍니다. 30일의 유예 기간이 지나면 webhook에서 작업을 받게 `Unsubscribe` 됩니다.

구독이 *일시 중단됨* 상태인 경우:

* 파트너 또는 ISV는 데이터 또는 설정 손실 없이 전체 기능을 복원할 수 있도록 SaaS 계정을 복구 가능한 상태로 유지해야 합니다.
* 파트너 또는 ISV는 유예 기간 동안 결제가 이루어지면 구독 복구 요청이, 유예 기간이 종료되면 구독 프로비전 해제 요청이 있을 것으로 예상해야 합니다. 두 요청은 모두 webhook 메커니즘을 통해 전송됩니다.

게시자가 어떤 조치를 취하기 전에는 구독 상태가 Microsoft 쪽에서 [일시 중단됨]으로 변경됩니다. 활성 구독만 일시 중단할 수 있습니다.

### <a name="reinstated-suspended"></a>복구됨(*일시 중단됨*)

이 작업은 고객의 결제 방법을 다시 사용할 수 있고, SaaS 구독 요금이 결제되었으며, 구독을 복구하는 중임을 나타냅니다. 이 경우 다음과 같습니다. 

1. Microsoft는 *action* 매개 변수가 *Reinstate* 값으로 설정된 webhook를 호출합니다.
1. 게시자는 구독이 게시자 쪽에서 다시 완전히 작동하는지 확인합니다.
1. 게시자가 성공 상태를 사용하여 [패치 작업 API](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation)를 호출합니다.
1. 복구 프로세스가 성공하고 고객에게 SaaS 구독 요금이 다시 청구됩니다. 

패치가 *실패* 상태와 함께 전송되면 복구 프로세스가 Microsoft 쪽에서 완료되지 않고 구독은 *일시 중단됨* 상태로 유지됩니다.

일시 중단된 구독만 복구할 수 있습니다. 일시 중단된 SaaS 구독은 복구되는 동안 *일시 중단됨* 상태로 유지됩니다. 이 작업이 완료되면 구독의 상태가 *활성* 으로 변경됩니다.

### <a name="renewed-subscribed"></a>갱신됨(*구독됨*)

SaaS 구독은 월간 또는 연간 구독 기간이 끝날 때 Microsoft에서 자동으로 갱신합니다. 모든 SaaS 구독의 자동 갱신 설정의 기본값은 *true* 입니다. 활성 SaaS 구독은 정기적으로 계속 갱신됩니다. Microsoft는 갱신 이벤트에 대 한 알림 전용 webhook 알림을 제공 합니다. 고객은 Microsoft 365 관리 포털을 통해 SaaS 구독의 자동 갱신을 해제할 수 있습니다. 이 경우 SaaS 구독은 현재 청구 기간 종료 시 자동으로 취소됩니다. 고객은 언제든지 SaaS 구독을 취소할 수 있습니다.

활성 구독만 자동으로 갱신됩니다. 갱신 프로세스가 진행되는 동안 그리고 자동 갱신이 성공하면 구독이 활성 상태를 유지합니다. 갱신 후 구독 기간의 시작 및 종료 날짜가 새 기간의 날짜로 업데이트됩니다.

결제 문제로 인해 자동 갱신이 실패하면 구독이 *일시 중단* 되고 게시자에게 알림이 전달됩니다.

### <a name="canceled-unsubscribed"></a>취소됨(*구독 취소됨*)

게시자 사이트, Azure Portal 또는 Microsoft 365 관리 센터에서 구독을 취소하면 명시적 고객 또는 CSP 작업에 대한 응답으로 구독이 이 상태에 도달합니다. 또한 *일시 중단됨* 상태로 전환된 후 30일이 지나도 요금 결제가 이루어지지 않으면 구독이 암시적으로 취소될 수 있습니다.

게시자는 취소 webhook 호출을 받은 후 최소 7일 동안 요청 시 복구할 수 있도록 고객 데이터를 보존해야 합니다. 이 기간이 지나야만 고객 데이터를 삭제할 수 있습니다.

SaaS 구독은 수명 주기 동안 언제든지 취소할 수 있습니다. 구독을 취소한 후에는 다시 활성화할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [SaaS 배송 구독 Api v2](pc-saas-fulfillment-subscription-api.md)
- [SaaS 처리 작업 Api v2](pc-saas-fulfillment-operations-api.md)
