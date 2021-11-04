---
title: 클라우드 용 Microsoft Defender에 관리 그룹 등록
description: 제공 된 Azure Policy 정의를 사용 하 여 관리 그룹의 모든 구독에 대해 Microsoft Defender for Cloud를 사용 하도록 설정 하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 88e57e988efbaaf0472bf809aeecb5ddeb12445c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422409"
---
# <a name="enable-defender-for-cloud-on-all-subscriptions-in-a-management-group"></a>관리 그룹의 모든 구독에서 클라우드에 대해 Defender를 사용 하도록 설정

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Policy를 사용 하 여 동일한 관리 그룹 (MG) 내의 모든 Azure 구독에서 클라우드 용 Microsoft Defender를 사용 하도록 설정할 수 있습니다. 이는 포털에서 개별적으로 액세스하는 것보다 편리하며, 구독이 다른 소유자에게 속한 경우에도 작동합니다. 

관리 그룹 및 모든 해당 구독을 온보딩하려면 다음을 수행합니다.

1. **보안 관리자** 권한이 있는 사용자는 Azure Policy를 열고 **구독에서 클라우드에 대 한 정의를 사용 하도록 설정** 을 검색 합니다.

    :::image type="content" source="./media/get-started/enable-security-center-policy.png" alt-text="Azure Policy 정의는 구독에서 클라우드에 대해 Defender를 사용 하도록 설정 합니다.":::

1. **할당** 을 선택하고, 범위를 MG 수준으로 설정했는지 확인합니다.

    :::image type="content" source="./media/get-started/assign-policy.png" alt-text="정의를 할당 하면 구독에서 클라우드에 대해 Defender를 사용할 수 있습니다.":::

    > [!TIP]
    > 범위 외에는 필수 매개 변수가 없습니다.

1. 클라우드에 대 한 Defender를 사용 하지 않는 기존 구독을 모두 사용 하도록 설정 하려면 **수정 작업 만들기** 를 선택 합니다. 그러면 등록이 수신 됩니다.

    :::image type="content" source="./media/get-started/remediation-task.png" alt-text="Azure Policy 정의에 대 한 수정 작업을 만들어 구독에서 클라우드에 대해 Defender를 사용 하도록 설정 합니다.":::

1. 정의가 할당되면 다음이 수행됩니다.

    1. 클라우드에 대 한 Defender에 아직 등록 되지 않은 MG의 모든 구독을 검색 합니다.
    1. 해당 구독을 "비준수"로 표시합니다.
    1. 클라우드의 향상 된 보안 기능에 대해 Defender가 있는지 여부에 관계 없이 등록 된 모든 구독을 "규격"으로 표시 합니다.

    그런 다음 재구성 작업은 비준수 구독에서 무료로 Defender for Cloud를 사용 하도록 설정 합니다.

> [!IMPORTANT]
> 정책 정의는 **기존** 구독 에서만 클라우드에 Defender를 사용 하도록 설정 합니다. 새로 만든 구독을 등록 하려면 준수 탭을 열고, 관련 비호환 구독을 선택 하 고, 수정 작업을 만듭니다. 클라우드 용 Defender를 사용 하 여 모니터링 하려는 새 구독이 하나 이상 있는 경우이 단계를 반복 합니다.

## <a name="optional-modifications"></a>선택적 수정

Azure Policy 정의를 수정하기 위해 선택할 수 있는 다양한 방법이 있습니다. 

- **준수 정의 다르게 정의** -제공 된 정책은 아직 Defender for Cloud에 등록 되지 않은 MG의 모든 구독을 "비규격"으로 분류 합니다. 클라우드의 강화 된 보안 기능을 사용 하도록 설정 하려면 Defender가 없는 모든 구독으로 설정 하도록 선택할 수 있습니다.

    제공된 정의는 아래 '가격 책정' 설정 중 *하나* 를 준수로 정의합니다. '표준' 또는 '체험'으로 설정된 구독은 규정을 준수함을 의미합니다.

    > [!TIP]
    > Microsoft Defender 계획을 사용 하도록 설정한 경우 정책 정의에서 ' 표준 ' 설정으로 설명 됩니다. 사용하지 않도록 설정되면 '체험'입니다. 이러한 계획 간의 차이점에 대 한 자세한 내용은 [클라우드의 향상 된 보안 기능에 대 한 Microsoft Defender](enhanced-security-features-overview.md)를 참조 하세요. 

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

- **클라우드에 대해 defender를 사용 하도록 설정할 때 적용할 일부 Microsoft Defender 계획 정의** -제공 된 정책은 선택적인 향상 된 보안 기능 없이 클라우드에 defender를 사용 하도록 설정 합니다. 하나 이상의 Microsoft Defender 계획을 사용 하도록 선택할 수 있습니다.

    제공된 정의의 `deployment` 섹션에는 `pricingTier` 매개 변수가 있습니다. 이는 기본적으로 `free`로 설정되지만 수정할 수 있습니다. 


## <a name="next-steps"></a>다음 단계:

이제 전체 관리 그룹을 등록 향상 된 보안 기능을 사용할 수 있습니다. 

> [!div class="nextstepaction"]
> [향상 된 보호 사용](enable-enhanced-security.md)