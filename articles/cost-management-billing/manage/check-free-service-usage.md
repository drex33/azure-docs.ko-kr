---
title: Azure 무료 서비스 사용량 모니터링 및 추적
description: Azure Portal에서 무료 서비스 사용량을 확인하는 방법에 대해 알아봅니다. 서비스 한도를 초과하지 않는 한 체험 계정에 포함된 서비스에 대해서는 요금이 청구되지 않습니다.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: c6a316d11f4d6d43561daab97e2b7b8d4f197a77
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610430"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Azure 무료 계정에 포함된 무료 서비스의 사용량 확인

서비스의 한도를 초과하지 않는 한, Azure 체험 계정에 무료로 포함된 서비스 요금은 청구되지 않습니다. 한도를 유지하기 위해 Azure Portal을 사용하여 무료 서비스 사용량을 추적할 수 있습니다.

## <a name="check-usage-in-the-azure-portal"></a>Azure Portal에서 사용량 확인

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
1.  **구독** 을 검색합니다.  
    ![포털의 구독 검색을 보여 주는 스크린샷](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  Azure 체험 계정에 가입할 때 생성된 구독을 선택합니다.
1.  아래로 스크롤하여 무료 서비스 사용량을 보여 주는 테이블을 찾습니다.  
    ![무료 서비스의 사용량을 보여 주는 스크린샷](./media/check-free-service-usage/subscription-usage-free-services.png)

표에는 다음과 같은 열이 있습니다.

* **미터:** 사용 중인 서비스에 대한 측정 단위를 식별합니다.
* **사용/한도:** 현재 월의 사용량 및 측정기의 제한입니다.
* **상태:** 서비스의 사용량 상태입니다. 사용량에 따라, 다음 상태 중 하나일 수 있습니다.
  * **사용 중이 아님:** 측정기를 사용하지 않았거나 측정기의 사용량이 청구 시스템에 연결되지 않았습니다.
  * **\<Date>에 초과됨:** \<Date>의 측정기 제한을 초과했습니다.
  * **초과할 가능성이 낮음:** 측정기의 제한을 초과할 가능성이 낮습니다.
  * **\<Date>에 초과:** \<Date>에 측정기 제한에 도달할 가능성이 높습니다.

> [!IMPORTANT]
>
> 무료 서비스는 Azure 체험 계정에 가입할 때 만든 구독에 대해서만 사용할 수 있습니다. 구독 개요 페이지에서 무료 서비스 테이블을 볼 수 없는 경우 구독에 사용할 수 없습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [Azure 체험 계정 업그레이드](upgrade-azure-subscription.md)
