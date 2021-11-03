---
title: Microsoft Defender for Cloud에 관리 그룹 온보딩
description: 제공된 Azure Policy 정의를 사용하여 관리 그룹의 모든 구독에 대해 Microsoft Defender for Cloud를 사용하도록 설정하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a39a46bbc78cb915e57f7e584c5c567ff8718ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064773"
---
# <a name="enable-defender-for-cloud-on-all-subscriptions-in-a-management-group"></a>관리 그룹의 모든 구독에서 Defender for Cloud 사용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Policy 사용하여 MG(동일한 관리 그룹) 내의 모든 Azure 구독에서 Microsoft Defender for Cloud를 사용하도록 설정할 수 있습니다. 이는 포털에서 개별적으로 액세스하는 것보다 편리하며, 구독이 다른 소유자에게 속한 경우에도 작동합니다. 

관리 그룹 및 모든 해당 구독을 온보딩하려면 다음을 수행합니다.

1. **보안 관리자** 권한이 있는 사용자는 Azure Policy 열고 구독에서 클라우드용 Defender 사용 정의를 **검색합니다.**

    :::image type="content" source="./media/get-started/enable-security-center-policy.png" alt-text="Azure Policy 정의 구독에서 Defender for Cloud를 사용하도록 설정합니다.":::

1. **할당** 을 선택하고, 범위를 MG 수준으로 설정했는지 확인합니다.

    :::image type="content" source="./media/get-started/assign-policy.png" alt-text="구독에서 Defender for Cloud 사용 정의를 할당합니다.":::

    > [!TIP]
    > 범위 외에는 필수 매개 변수가 없습니다.

1. **수정 작업 만들기를** 선택하여 Defender for Cloud를 사용하도록 설정하지 않은 모든 기존 구독이 온보딩되도록 합니다.

    :::image type="content" source="./media/get-started/remediation-task.png" alt-text="구독에서 Azure Policy 정의 Enable Defender for Cloud에 대한 수정 작업을 만듭니다.":::

1. 정의가 할당되면 다음이 수행됩니다.

    1. Defender for Cloud에 아직 등록되지 않은 MG의 모든 구독을 검색합니다.
    1. 해당 구독을 "비준수"로 표시합니다.
    1. 등록된 모든 구독을 "준수"로 표시합니다(Defender for Cloud의 향상된 보안 기능 사용 여부에 관계없이).

    그런 다음 수정 작업은 비준수 구독에서 무료로 Defender for Cloud를 사용하도록 설정합니다.

> [!IMPORTANT]
> 정책 정의는 기존 구독에서만 Defender for **Cloud를** 사용하도록 설정합니다. 새로 만든 구독을 등록하려면 준수 탭을 열고 관련 비준수 구독을 선택한 다음 수정 작업을 만듭니다. Defender for Cloud를 사용하여 모니터링하려는 새 구독이 하나 이상 있는 경우 이 단계를 반복합니다.

## <a name="optional-modifications"></a>선택적 수정

Azure Policy 정의를 수정하기 위해 선택할 수 있는 다양한 방법이 있습니다. 

- **규정 준수를 다르게 정의** - 제공된 정책은 Defender for Cloud에 아직 등록되지 않은 MG의 모든 구독을 "비규격"으로 분류합니다. Defender for Cloud의 향상된 보안 기능을 사용하지 않고 모든 구독으로 설정하도록 선택할 수 있습니다.

    제공된 정의는 아래 '가격 책정' 설정 중 *하나* 를 준수로 정의합니다. '표준' 또는 '체험'으로 설정된 구독은 규정을 준수함을 의미합니다.

    > [!TIP]
    > Microsoft Defender 플랜을 사용하도록 설정하면 정책 정의에서 '표준' 설정에 있는 것으로 설명됩니다. 사용하지 않도록 설정되면 '체험'입니다. 이러한 계획의 차이점에 대한 자세한 내용은 [클라우드용 Microsoft Defender의 향상된 보안 기능을 참조하세요.](enhanced-security-features-overview.md) 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    이를 다음으로 변경하면 '표준'으로 설정된 구독만 준수로 분류됩니다.

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Defender for Cloud를 사용하도록 설정할 때 적용할 일부 Microsoft Defender 계획 정의** - 제공된 정책은 선택적인 향상된 보안 기능 없이 Defender for Cloud를 사용하도록 설정합니다. Microsoft Defender 플랜 중 하나 이상을 사용하도록 선택할 수 있습니다.

    제공된 정의의 `deployment` 섹션에는 `pricingTier` 매개 변수가 있습니다. 이는 기본적으로 `free`로 설정되지만 수정할 수 있습니다. 


## <a name="next-steps"></a>다음 단계:

이제 전체 관리 그룹을 온보딩했으므로 향상된 보안 기능을 사용하도록 설정합니다. 

> [!div class="nextstepaction"]
> [향상된 보호 사용](enable-enhanced-security.md)
