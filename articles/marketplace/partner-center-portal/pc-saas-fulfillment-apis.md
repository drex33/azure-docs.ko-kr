---
title: Microsoft 상업용 Marketplace의 SaaS 처리 API
description: Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품을 통합하는 데 사용할 수 있는 처리 API를 소개합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: saasguide
ms.author: souchak
ms.openlocfilehash: 50bb572d7fc9c05bb22c8ab35851df7a58dc2562
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455941"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>Microsoft 상업용 Marketplace의 SaaS 처리 API

SaaS 처리 API를 사용하면 게시자 또는 ISV(Independent Software Vendor)가 Microsoft AppSource, Azure Marketplace, Azure Portal에서 해당 SaaS 애플리케이션을 게시하고 판매할 수 있습니다. 해당 API를 사용하면 ISV 애플리케이션이 direct, partner-led(재판매인), field-led 같은 모든 상거래 지원 채널에 참여할 수 있습니다.  파트너 센터에서 거래 가능한 SaaS 제품을 만들고 게시하려면 이 API와 통합해야 합니다.

ISV 및 Microsoft 둘 다에서 동일한 구독 상태를 유지 관리하려면 ISV는 SaaS 서비스 코드에 추가하여 다음 API 흐름을 구현해야 합니다.

* 방문 페이지 흐름: Microsoft는 마켓플레이스에서 고객이 게시자의 SaaS 제품을 구매한 것을 게시자에게 알립니다.
* 활성화 흐름: 게시자는 새로 구매한 SaaS 계정이 게시자 쪽에서 구성되었음을 Microsoft에 알립니다.
* 업데이트 흐름: 구매한 플랜 및/또는 구매한 사용자 수를 변경합니다.
* 일시 중단 및 복구 흐름: 고객의 결제 방법이 더 이상 유효하지 않을 경우 구매한 SaaS 제품을 일시 중단합니다. 결제 방법에 대한 문제가 해결되면 일시 중단된 제품을 복구할 수 있습니다.
* 웹후크 흐름: Microsoft 쪽에서 고객이 트리거한 SaaS 구독 변경 내용 및 취소에 대해 Microsoft가 게시자에게 알립니다.

구매한 SaaS 구독을 취소하는 경우 통합은 선택 사항입니다. Microsoft 쪽에서 고객이 수행할 수 있기 때문입니다.

SaaS 처리 API와 올바르게 통합하는 것은 다음을 보장하는 데 중요합니다.

* 게시자의 SaaS 제품을 구매한 최종 고객에게는 Microsoft를 통해 올바르게 요금이 청구됩니다.
* 최종 고객은 마켓플레이스에서 구매한 SaaS 구독을 구매하고, 구성하고, 사용하고, 관리하는 데 적합한 사용자 환경을 제공합니다.

이 API를 사용하면 게시자의 제품이 다음과 같은 모든 상거래 지원 채널에 참여할 수 있습니다.

* direct
* partner-led(재판매인, CSP)
* field-led

재판매인(CSP) 시나리오에서는 CSP가 최종 고객을 대신하여 SaaS 제품을 구매합니다. 고객이 SaaS 제품을 사용해야 하지만 CSP인 엔터티는 다음을 수행합니다.

* 고객 요금 청구
* 구독 플랜/구매한 사용자 수 변경
* 구독 취소

이 시나리오에서는 게시자가 API 호출 흐름을 다르게 구현할 필요가 없습니다.

CSP에 대한 자세한 내용은 https://partner.microsoft.com/licensing 을 참조하세요.

>[!Warning]
>이 API의 현재 버전은 모든 새 SaaS 제품에 사용되는 버전 2입니다. API 버전 1은 더 이상 사용되지 않으며 기존 제품을 지원하기 위해 유지 관리되고 있습니다.

>[!Note]
>SaaS 처리 API는 게시자의 백 엔드 서비스에서 호출되는 용도로만 사용됩니다. 게시자의 웹 페이지에서 직접 API와 통합하는 것은 지원되지 않습니다. 서비스 간 인증 흐름만 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않은 경우 [Azure AD 애플리케이션 등록](./pc-saas-registration.md)에 설명된 대로 [Azure Portal](https://ms.portal.azure.com)에서 SaaS 애플리케이션을 등록합니다.  그런 다음, 이 인터페이스의 최신 버전인 [SaaS 처리 API 버전 2](./pc-saas-fulfillment-api-v2.md)를 개발에 사용합니다.
