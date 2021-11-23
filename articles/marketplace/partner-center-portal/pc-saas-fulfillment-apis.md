---
title: Microsoft 상업용 Marketplace의 SaaS 처리 API
description: Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품을 통합하는 데 사용할 수 있는 처리 API를 소개합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2021
author: arifgani
ms.author: argani
ms.openlocfilehash: b901643cfe480a9c3e2438e39697a8d56baaeab0
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132935373"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Microsoft 상업용 Marketplace의 SaaS 처리 API

SaaS 처리 API를 사용하면 게시자 또는 ISV(Independent Software Vendor)가 Microsoft AppSource, Azure Marketplace, Azure Portal에서 해당 SaaS 애플리케이션을 게시하고 판매할 수 있습니다. 해당 API를 사용하면 ISV 애플리케이션이 direct, partner-led(재판매인), field-led 같은 모든 상거래 지원 채널에 참여할 수 있습니다. 파트너 센터에서 거래 가능한 SaaS 제품을 만들고 게시하려면 이 API와 통합해야 합니다.

SaaS 처리 API에 대해 알아보려면 다음 문서도 읽어보는 것이 좋습니다.
- [SaaS 구독 수명 주기 관리](pc-saas-fulfillment-life-cycle.md)
- [SaaS 처리 구독 API v2](pc-saas-fulfillment-subscription-api.md)
- [SaaS 처리 작업 API v2](pc-saas-fulfillment-operations-api.md)
- [SaaS 서비스에서 webhook 구현](pc-saas-fulfillment-webhook.md)
- [SaaS 제공 API에 대한 일반적인 질문](saas-fulfillment-apis-faq.yml)

## <a name="api-flows"></a>API 흐름

ISV 및 Microsoft 둘 다에서 동일한 구독 상태를 유지 관리하려면 ISV는 SaaS 서비스 코드에 추가하여 다음 API 흐름을 구현해야 합니다.

* 방문 페이지 흐름: Microsoft는 마켓플레이스에서 고객이 게시자의 SaaS 제품을 구매한 것을 게시자에게 알립니다.
* 활성화 흐름: 게시자는 새로 구매한 SaaS 계정이 게시자 쪽에서 구성되었음을 Microsoft에 알립니다.
* 업데이트 흐름: 구매한 플랜 또는 구매한 시트 수 또는 둘 다 변경합니다.
* 일시 중단 및 복구 흐름: 고객의 결제 방법이 더 이상 유효하지 않을 경우 구매한 SaaS 제품을 일시 중단합니다. 결제 방법에 대한 문제가 해결되면 일시 중단된 제품을 복구할 수 있습니다.
* Webhook 흐름: Microsoft는 Microsoft 쪽에서 고객이 트리거한 SaaS 구독 변경 및 취소에 대해 게시자에게 알립니다.

구매한 SaaS 구독을 취소하는 경우 통합은 선택 사항입니다. Microsoft 쪽에서 고객이 수행할 수 있기 때문입니다.

SaaS Fulfillment API와의 올바른 통합은 다음을 확인하는 데 중요합니다.

* 게시자의 SaaS 제안을 구매한 최종 고객은 Microsoft에서 올바르게 청구됩니다.
* 최종 고객은 마켓플레이스에서 구매한 SaaS 구독을 구매, 구성, 사용 및 관리하는 올바른 사용자 환경을 얻게 됩니다.

이 API를 사용하면 게시자의 제품이 다음과 같은 모든 상거래 지원 채널에 참여할 수 있습니다.

* 직접
* 파트너 주도(대리점, CSP)
* 필드 주도

재판매인(CSP) 시나리오에서는 CSP가 최종 고객을 대신하여 SaaS 제품을 구매합니다. 고객은 SaaS 제안을 사용해야 하지만 CSP는 다음 작업을 수행하는 엔터티입니다.

* 고객에게 청구
* 구독 계획/구매한 시트의 양을 변경합니다.
* 구독을 취소합니다.

이 시나리오에서는 게시자가 API 호출 흐름을 다르게 구현할 필요가 없습니다.

CSP에 대한 자세한 내용은 를 https://partner.microsoft.com/licensing 참조하세요.

>[!Warning]
>이 API의 현재 버전은 모든 새 SaaS 제품에 사용되는 버전 2입니다. API 버전 1은 더 이상 사용되지 않으며 기존 제품을 지원하기 위해 유지 관리되고 있습니다.

>[!Note]
>SaaS 처리 API는 게시자의 백 엔드 서비스에서 호출되는 용도로만 사용됩니다. 게시자의 웹 페이지에서 직접 API와 통합하는 것은 지원되지 않습니다. 서비스 간 인증 흐름만 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- 아직 수행하지 않은 경우 [Azure AD 애플리케이션 등록](./pc-saas-registration.md)에 설명된 대로 [Azure Portal](https://ms.portal.azure.com)에서 SaaS 애플리케이션을 등록합니다.  그런 다음 [SaaS 처리 구독 API v2 및 SaaS 처리 작업 API v2와](pc-saas-fulfillment-subscription-api.md) 같은 최신 버전의 이 [인터페이스를](pc-saas-fulfillment-operations-api.md)개발에 사용합니다.
