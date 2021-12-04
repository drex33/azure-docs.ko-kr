---
title: Azure 무료 서비스 사용량 모니터링 및 추적
description: Azure Portal에서 무료 서비스 사용량을 확인하는 방법에 대해 알아봅니다. 서비스 한도를 초과하지 않는 한 체험 계정에 포함된 서비스에 대해서는 요금이 청구되지 않습니다.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/03/2021
ms.author: banders
ms.openlocfilehash: 60ba648a31662c9e9cbcf5bd25e392c667459629
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133543264"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Azure 무료 계정에 포함된 무료 서비스의 사용량 확인

서비스의 한도를 초과하지 않는 한, Azure 체험 계정에 무료로 포함된 서비스 요금은 청구되지 않습니다. 한도를 유지하기 위해 Azure Portal을 사용하여 무료 서비스 사용량을 추적할 수 있습니다.

사용 가능한 리소스 사용을 시작한 후에만 Azure Portal에 표시 되므로 리소스를 사용 하지 않은 경우 사용이 표시 되지 않습니다. 사용 및 상태는 즉시 표시 되지 않습니다. 리소스를 사용한 후 1 ~ 2 일 동안 지연 됩니다.

## <a name="check-usage-in-the-azure-portal"></a>Azure Portal에서 사용량 확인

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
1.  **구독** 을 검색합니다.  
    ![포털의 구독 검색을 보여 주는 스크린샷](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  Azure 체험 계정에 가입할 때 생성된 구독을 선택합니다.
1.  이 페이지에는 무료 서비스 사용이 표시 됩니다.  
    :::image type="content" source="./media/check-free-service-usage/free-account-subscription-page.png" alt-text="Azure 사용량이 표시 되는 무료 계정 구독 페이지를 보여 주는 스크린샷" lightbox="./media/check-free-service-usage/free-account-subscription-page.png" :::

페이지의 사용 영역은 다음과 같습니다.

- **지출 율 및 예측** -시간이 지남에 따라 사용한 리소스의 비용을 보여 줍니다. **세부 정보 보기** 를 선택 하 여 비용 분석에서 지출에 대 한 자세한 정보를 확인 합니다.
- **자원별 비용** -사용한 개별 리소스에 대 한 비용을 표시 합니다. **세부 정보 보기** 를 선택 하 여 비용 분석에서 지출에 대 한 자세한 정보를 확인 합니다.
- **사용량 별 상위 무료 서비스** -영역은 무료 계정에 대해서만 표시 되므로 무료 계정이 없으면 영역이 표시 되지 않습니다. 이 영역에는 사용한 무료 서비스가 표시 됩니다. **모든 무료 서비스 보기** 를 선택 하 여 **12 개월 무료 서비스** 테이블에서 다음 열을 표시 합니다.
    - **측정기** -사용 중인 서비스에 대 한 측정 단위를 식별 합니다.
    - **사용량/제한** -현재 월의 사용량과 미터에 대 한 제한입니다.
    - **상태** -서비스의 사용 상태입니다. 사용량에 따라, 다음 상태 중 하나일 수 있습니다.
        - **사용 하지 않음** -측정기를 사용 하지 않았거나 측정기 사용이 청구 시스템에 도달 하지 않았습니다.
        - **초과 \<Date>** 됨 -측정기에 대 한 제한을 초과 했습니다 \<Date> .
        - **초과할 가능성이 낮음** -미터의 제한을 초과할 가능성이 낮습니다.
        - **초과 \<Date>** -측정기에 대 한 한도를 초과할 가능성이 높습니다 \<Date> .  
            :::image type="content" source="./media/check-free-service-usage/free-services-table.png" alt-text="12 개월간 무료 서비스 테이블을 보여 주는 스크린샷" lightbox="./media/check-free-service-usage/free-services-table.png" :::
- **리소스 수 별 상위 제품** -사용한 제품 리소스의 가장 많은 수를 표시 합니다.
- **Azure defender 검사** -azure defender 사용량을 표시 합니다. 30 일 무료 평가판을 시작 하 여 Microsoft Defender를 사용 하도록 설정 하려면 **업그레이드 범위** 를 선택 합니다.

> [!IMPORTANT]
>
> 무료 서비스는 Azure 체험 계정에 가입할 때 만든 구독에 대해서만 사용할 수 있습니다. 구독 개요 페이지에서 무료 서비스 테이블을 볼 수 없는 경우 구독에 사용할 수 없습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [Azure 체험 계정 업그레이드](upgrade-azure-subscription.md)
