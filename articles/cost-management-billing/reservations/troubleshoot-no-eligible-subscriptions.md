---
title: Azure Portal에서 적격 구독 없음 문제 해결
description: 이 문서는 예약을 구매하려고 할 때 Azure Portal에서 적격 구독 없음 오류 메시지 문제를 해결하는 데 도움이 됩니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 06/27/2021
ms.openlocfilehash: 40002ab69c39481393654629a44a038dfc9d01af
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988934"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>적격 구독 없음 문제 해결

이 문서는 예약을 구매하려고 할 때 Azure Portal에서 *적격 구독 없음* 오류 메시지 문제를 해결하는 데 도움이 됩니다.

## <a name="symptoms"></a>증상

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **예약** 으로 이동합니다.
1. **추가**, 서비스를 차례로 선택합니다.
1. 다음 오류 메시지가 표시됩니다.
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. **구매할 제품 선택** 영역에서 **청구 구독** 목록을 펼쳐서 특정 구독에서 예약 인스턴스를 구매할 수 없는 이유를 확인합니다. 다음 이미지에서는 예약을 구매할 수 없는 예를 보여 줍니다.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="예약을 구매할 수 없는 이유를 보여 주는 예" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>원인

Azure 예약 인스턴스를 구매하려면 다음 요구 사항을 충족하는 하나 이상의 구독이 있어야 합니다.

- 구독은 지원되는 제안 유형이어야 합니다. 지원되는 제안 유형은 종량제, CSP(클라우드 솔루션 공급자), Microsoft Azure 엔터프라이즈 또는 Microsoft 고객 계약입니다.
- 구독의 소유자 또는 예약 구매자여야 합니다.

요구 사항을 충족하는 구독이 없는 경우 `No eligible subscriptions` 오류가 발생합니다.

### <a name="cause-1"></a>원인 1

구독은 지원되는 제안 유형이어야 합니다. 지원되는 제안 유형은 종량제, CSP, Microsoft Azure 엔터프라이즈 또는 Microsoft 고객 계약입니다. 구독 유형이 지원되지 않는 유형입니다. 예약을 지원하지 않는 제안 유형이 있는 구독을 선택하면 다음과 같은 오류가 표시됩니다.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="'구독으로 구입할 수 없음' 오류 메시지를 보여 주는 예" :::

### <a name="cause-2"></a>원인 2

구독의 소유자 또는 예약 구매자여야 합니다. 충분한 권한이 없으면 다음과 같은 오류가 표시됩니다.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>해결 방법

- 현재 제안에서 예약을 지원하지 않는 경우 새 Azure 구독을 만들어야 합니다.
- 기존 예약에 액세스할 수 없는 경우 현재 소유자로부터 해당 예약에 대한 액세스 권한을 얻을 수 있습니다.

### <a name="solution-1"></a>해결 방법 1

예약을 구매하려면 예약을 지원하는 새 Azure 구독을 만들어야 합니다.

- Azure 평가판을 사용하는 경우 [구독을 업그레이드](../manage/upgrade-azure-subscription.md)할 수 있습니다.
- 새 Azure 구독은 [종량제 요금](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)을 사용하여 만들 수 있습니다.
- [Microsoft 고객 계약](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)에 가입하고 새 구독을 만듭니다.
- [CSP](https://www.microsoft.com/solution-providers/home)를 사용하여 새 구독을 구매하고 새 구독을 만듭니다.

### <a name="solution-2"></a>해결 방법 2

예약에 대한 소유자 권한을 얻으려면 다음 중 하나에 액세스해야 합니다.

- 예약을 구매한 예약 주문
- 예약 자체

현재 예약 주문 소유자 또는 예약 소유자는 다음 단계를 사용하여 액세스 권한을 위임할 수 있습니다.

다른 사용자가 예약을 관리할 수 있게 하려면 다음 두 가지 옵션을 사용하면 됩니다.

- 예약 주문의 리소스 범위에 있는 사용자에게 소유자 역할을 할당하여 개별 예약 주문에 대한 액세스 관리를 위임합니다. 제한된 액세스 권한을 부여하려면 다른 역할을 선택합니다.  
     세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

- 기업계약 또는 Microsoft 고객 계약에 사용자를 청구 관리자로 추가합니다.
    - 기업계약의 경우 _엔터프라이즈 관리자_ 역할이 있는 사용자를 추가하여 기업계약에 적용되는 모든 예약 주문을 보고 관리합니다. _엔터프라이즈 관리자(읽기 전용)_ 역할이 있는 사용자는 예약만 볼 수 있습니다. 부서 관리자 및 계정 소유자는 액세스 제어(IAM)를 사용하여 명시적으로 추가하지 _않는 한_ 예약을 볼 수 없습니다. 자세한 내용은 [Azure Enterprise 역할 관리](../manage/understand-ea-roles.md)를 참조하세요.

        _엔터프라이즈 관리자는 예약 주문의 소유권을 가질 수 있으며, 액세스 제어(IAM)를 사용하여 예약에 다른 사용자를 추가할 수 있습니다._
    - Microsoft 고객 계약의 경우 청구 프로필 소유자 역할 또는 청구 프로필 기여자 역할을 가진 사용자는 청구 프로필을 사용하여 이루어진 모든 예약 구매를 관리할 수 있습니다. 청구 프로필 리더와 청구서 관리자는 청구 프로필로 지불되는 모든 예약을 볼 수 있습니다. 그러나 예약을 변경할 수는 없습니다.
    자세한 내용은 [청구 프로필 역할 및 작업](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

자세한 내용은 [예약을 관리할 수 있는 사용자 추가 또는 변경](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 예약 소유자에게 액세스 권한을 부여해야 하는 경우 [예약을 관리할 수 있는 사용자 추가 또는 변경](manage-reserved-vm-instance.md#who-can-manage-a-reservation-by-default)을 검토합니다.
