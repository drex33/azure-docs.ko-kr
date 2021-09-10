---
title: 비활성화된 Azure 구독 다시 활성화
description: 사용하지 않도록 설정한 Azure 구독을 다시 활성화하는 방법을 설명합니다.
keywords: 비활성화된 Azure 구독
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/15/2021
ms.author: banders
ms.openlocfilehash: 1e669a794abe848d326a6d08156621dcf3883ec5
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292240"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>비활성화된 Azure 구독 다시 활성화

크레딧이 만료되거나, 사용자가 지출 한도에 도달하거나, 지연된 지불이 있거나, 신용 카드 제한에 도달하거나 계정 관리자가 구독을 취소한 경우 Azure 구독을 사용할 수 없습니다. 어떤 문제가 적용되는지 확인하고 이 문서의 단계에 따라 구독을 다시 활성화하세요.

## <a name="your-credit-is-expired"></a>크레딧이 만료됨

Azure 체험 계정에 등록하면 30일 동안 청구 통화로 200달러 Azure 크레딧과 12개월 무료 서비스를 제공하는 평가판 구독을 받습니다. 30일이 만료된 경우 Azure는 구독을 비활성화합니다. 구독에 포함된 크레딧과 체험 서비스를 벗어나는 사용량에 대한 요금을 실수로 부과하지 않도록 보호하기 위해 구독이 비활성화됩니다. Azure 서비스를 계속 사용하려면[구독을 업그레이드](upgrade-azure-subscription.md)해야 합니다. 업그레이드한 후에도 구독은 12개월 동안 체험 서비스에 액세스할 수 있습니다. 체험 서비스 수량 제한을 초과한 사용량에 대해서만 요금이 청구됩니다.

## <a name="you-reached-your-spending-limit"></a>지출 한도에 도달했습니다.

평가판 및 Visual Studio Enterprise와 같은 크레딧이 포함된 Azure 구독에는 지출 한도가 있습니다. 포함된 크레딧까지만 서비스를 사용할 수 있습니다. 사용량이 지출 한도에 도달하면 Azure는 해당 요금 청구 기간의 나머지 기간에 대해 구독을 비활성화합니다. 구독에 포함된 크레딧을 벗어나는 사용량에 대한 요금을 실수로 부과하지 않도록 보호하기 위해 구독이 비활성화됩니다. 지출 한도를 제거하려면 [Azure Portal에서 지출 한도 제거](spending-limit.md#remove)를 참조하세요.

> [!NOTE]
> 평가판 구독을 사용하고 지출 한도를 제거한 경우 평가판이 끝날 때 구독은 종량제 요금을 사용하는 개별 구독으로 변환됩니다. 구독을 만든 날로부터 30일 동안 남은 크레딧이 유지됩니다. 12개월 동안 체험 서비스에 액세스할 수 있습니다.

Azure에 대한 청구 작업을 모니터링하고 관리하려면 [Azure 비용 관리 계획](../understand/plan-manage-costs.md)을 참조하세요.

## <a name="your-bill-is-past-due"></a>청구 기한 경과

연체 잔액을 해결하려면 다음 문서 중 하나를 참조하세요.

- 종량제를 포함한 Microsoft 온라인 구독 프로그램 구독의 경우 [Azure에서 이메일을 받은 후 Azure 구독의 연체 잔액 해결](resolve-past-due-balance.md)을 참조하세요.
- Microsoft 고객 계약 구독의 경우 [Microsoft Azure에 대한 청구서를 지불하는 방법](../understand/pay-bill.md)을 참조하세요.

## <a name="the-bill-exceeds-your-credit-card-limit"></a>신용 카드 한도 초과

이 문제를 해결하려면 [다른 신용 카드로 전환](change-credit-card.md)합니다. 또한 비즈니스를 대표하는 경우 [청구서로 지불하도록 전환](pay-by-invoice.md)할 수 있습니다.

## <a name="the-subscription-was-accidentally-canceled"></a>구독이 실수로 취소되었습니다.

계정 관리자인데 실수로 종량제 구독을 취소한 경우 Azure Portal에서 다시 활성화할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 구독으로 이동한 다음, 취소된 구독을 선택합니다.
1. **다시 활성화** 를 선택합니다.
1. **확인** 을 선택하여 다시 활성화를 확인합니다.  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="다시 활성화 확인을 보여주는 스크린샷" :::

다른 구독 유형의 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 구독을 다시 활성화합니다.

## <a name="after-reactivation"></a>재활성화 후

구독을 다시 활성화한 후에는 리소스를 만들거나 관리하는 데 지연이 발생할 수 있습니다. 지연 시간이 30분을 초과하는 경우 [Azure 청구 지원](https://go.microsoft.com/fwlink/?linkid=2083458)에 도움을 요청하세요. 대부분의 Azure 리소스는 자동으로 다시 시작되며 어떤 조치도 필요하지 않습니다. 그러나 Azure 서비스 리소스를 확인하고 자동으로 다시 시작되지 않는 모든 리소스를 다시 시작하는 것이 좋습니다.

## <a name="upgrade-a-disabled-free-account"></a>사용하지 않는 무료 계정 업그레이드

무료가 아닌 리소스를 사용하고 크레딧이 부족하여 구독을 사용하지 않도록 설정한 다음 구독을 업그레이드하면, 업그레이드 후에 리소스가 사용하도록 설정됩니다. 이 경우 사용된 리소스에 대한 요금이 청구됩니다. 무료 계정 업그레이드에 대한 자세한 내용은 [Azure 계정 업그레이드](upgrade-azure-subscription.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [Azure 비용 관리 계획](../understand/plan-manage-costs.md) 방법을 알아봅니다.
