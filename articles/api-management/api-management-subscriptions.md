---
title: Azure API Management의 구독 | Microsoft Docs
description: Azure API Management의 구독 개념에 대해 알아봅니다. 소비자는 Azure API Management에서 구독을 사용하 여 API에 액세스할 수 있습니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/27/2021
ms.author: apimpm
ms.openlocfilehash: 9bbdc914eae50a7226b22952b40cdb4b28849239
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111672"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API Management의 구독

Azure API Management에서 구독은 API 소비자가 API Management 인스턴스를 통해 게시된 API에 액세스하는 가장 일반적인 방법입니다. 이 문서는 개념에 대한 개요를 제공합니다.

## <a name="what-are-subscriptions"></a>구독이란?

API Management를 통해 API를 게시하면 구독 키를 사용하여 API 액세스를 쉽게 보호할 수 있습니다. 해당 API를 호출할 때 HTTP 요청에 유효한 구독 키를 포함하여 게시된 API를 사용합니다. 유효한 구독 키가 없으면 호출은 다음을 수행합니다.
* API Management 게이트웨이에서 즉시 거부됩니다. 
* 백 엔드 서비스로 전달되지 않습니다.

API에 액세스하려면 구독과 구독 키가 필요합니다. *구독* 은 구독 키 쌍의 이름을 지정한 컨테이너입니다. 

정기적으로 키를 다시 생성하는 것은 일반적인 보안 예방 조치이므로 구독 키가 필요한 대부분의 Azure 제품은 키를 쌍으로 생성합니다. 서비스를 사용하는 각 애플리케이션은 *키 A* 에서 *키 B* 로 전환하고 최소한의 중단으로 키 A를 재생성할 수 있으며 그 반대의 경우도 마찬가지입니다. 

기타

* 개발자는 API 게시자의 승인 없이 구독을 받을 수 있습니다. 
* API 게시자는 API 소비자를 위해 직접 구독을 만들 수도 있습니다.

> [!TIP]
> API Management는 또한 다음 예를 포함한 기타 API 액세스 보호 메커니즘도 지원합니다.
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [클라이언트 인증서](api-management-howto-mutual-certificates-for-clients.md)
> - [호출자 IP 제한](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>구독 범위

구독은 다양한 범위, 즉 제품, 모든 API 또는 개별 API와 연결할 수 있습니다.

### <a name="subscriptions-for-a-product"></a>제품에 대한 범위

일반적으로 API Management의 구독은 단일 [API 제품](api-management-terminology.md) 범위와 연결되어 있습니다. 개발자:
* 개발자 포털에서 제품 목록을 찾았다고 가정해 보겠습니다. 
* 사용할 제품에 대한 구독 요청을 제출했습니다. 
* 해당 구독의 키(자동 또는 API 게시자 승인)를 사용하여 제품의 모든 API에 액세스합니다. 
    * 구독 범위(제품, 글로벌 또는 API)에 관계없이 구독 키가 있거나 없는 API에 액세스할 수 있습니다.

현재 개발자 포털에는 **사용자 프로필** 섹션에서 제품 범위 구독만 표시됩니다. 

> [!NOTE]
> API 범위 구독 키를 사용하는 경우 제품 범위에서 구성된 모든 *정책* 이 해당 구독에 적용되지 않습니다.

![제품 구독](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> 특정 시나리오에서는 API 게시자가 구독 요건 없이 API 제품을 대중에 공개하길 원할 수 있습니다. Azure Portal에서 제품의 **설정** 페이지에서 **구독 필요** 옵션을 선택 취소할 수 있습니다. 결과적으로, 제품의 모든 API에 API 키 없이 액세스할 수 있습니다.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>모든 API 또는 개별 API에 대한 구독

API Management의 [소비](https://aka.ms/apimconsumptionblog) 계층이 추가되어 구독 키 관리가 더욱 간소화되었습니다. 

#### <a name="two-more-subscription-scopes"></a>두 가지 추가 구독 범위

구독 범위가 더 이상 API 제품으로 제한되지 않으므로 다음 중 하나에 대한 액세스 권한을 부여하는 키를 만들 수 있습니다.
* 단일 API 또는 
* API Management 인스턴스 내의 모든 API. 

API를 추가하기 전에 제품을 만들 필요가 없습니다. 

각 API Management 인스턴스는 이제 변경 불가능한 모든 API 구독과 함께 제공됩니다. 이 구독을 사용하면 테스트 콘솔 내에서 더욱 쉽고 간단하게 API를 테스트하고 디버그할 수 있습니다.

#### <a name="standalone-subscriptions"></a>독립 실행형 구독

API Management는 이제 *독립 실행형* 구독을 허용합니다. 더 이상 구독을 개발자 계정과 연결할 필요가 없습니다. 이 기능은 구독을 공유하는 여러 개발자 또는 팀과 유사한 시나리오에서 유용합니다.

소유자를 지정하지 않고 구독을 만들면 독립 실행형 구독이 됩니다. 개발자와 나머지 팀에 독립 실행형 구독 키에 대한 액세스 권한을 부여하려면 다음 중 하나를 수행합니다.
* 구독 키를 수동으로 공유합니다.
* 사용자 지정 시스템을 사용하여 팀에서 구독 키를 사용할 수 있도록 합니다.

#### <a name="creating-subscriptions-in-azure-portal"></a>Azure Portal에서 구독 만들기

이제 API 게시자가 Azure Portal에서 직접 [구독을 생성](api-management-howto-create-subscriptions.md)할 수 있습니다.

![유연성 있는 구독](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>다음 단계
API Management에 대한 자세한 내용 확인:

+ API Management의 기타 [개념](api-management-terminology.md)에 대해 알아보기
+ [자습서](import-and-publish.md)에 따라 API Management에 대해 자세히 알아보기
+ 일반적인 질문은 [FAQ 페이지](api-management-faq.yml) 확인
