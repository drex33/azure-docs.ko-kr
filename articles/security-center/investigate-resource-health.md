---
title: '자습서: 리소스 상태 조사 - Microsoft Defender for Cloud'
description: '자습서: Microsoft Defender for Cloud를 사용하여 리소스의 상태를 조사하는 방법을 알아봅니다.'
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f156b8f1102e55ca56085aec7b63a1fdb2716450
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075817"
---
# <a name="tutorial-investigate-the-health-of-your-resources"></a>자습서: 리소스 상태 조사

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!NOTE]
> 이 자습서에서 설명하는 리소스 상태 페이지는 미리 보기 릴리스입니다.
> 
> [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] | |Pricing:|**Microsoft Defender for DNS** 는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.

리소스 상태 페이지는 단일 리소스의 전반적인 상태에 대한 스냅샷 보기를 제공합니다. 리소스에 대한 자세한 정보와 해당 리소스에 적용되는 모든 권장 사항을 검토할 수 있습니다. 또한 [Microsoft Defender의 고급 보호 계획](defender-for-cloud-introduction.md)을 사용하는 경우 해당 특정 리소스의 미해결 보안 경고도 볼 수 있습니다.

Defender for Cloud의 포털 페이지에서 현재 미리 보기로 제공되는 이 단일 페이지는 다음을 보여줍니다.

1. **리소스 정보** - 리소스 그룹 및 연결된 구독, 지리적 위치 등입니다.
1. **적용된 보안 기능** - 리소스에 Microsoft Defender 계획이 설정되어 있는지 여부입니다.
1. **미해결 권장 사항 및 경고 수** - 미해결 보안 권장 사항 및 Defender for Cloud 경고의 수입니다.
1. **실행 가능한 권장 사항 및 경고** - 리소스에 적용되는 권장 사항 및 경고를 나열하는 두 개의 탭입니다.

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="가상 머신의 상태 정보를 보여주는 Microsoft Defender for Cloud의 리소스 상태 페이지":::

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 모든 리소스 종류에 대한 리소스 상태 페이지에 액세스
> * 리소스의 미해결 보안 문제 평가
> * 리소스의 보안 상태 개선

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명하는 기능을 단계별로 실행하려면 다음을 수행합니다.

- Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- 보안 권장 사항을 적용하려면 관련 권한이 있는 계정(리소스 그룹 기여자, 리소스 그룹 소유자, 구독 기여자 또는 구독 소유자)으로 로그인해야 합니다.
- 경고를 해제하려면 관련 권한이 있는 계정(보안 관리자, 구독 기여자 또는 구독 소유자)으로 로그인해야 합니다.

##  <a name="access-the-health-information-for-a-resource"></a>리소스의 상태 정보에 액세스

> [!TIP]
> 아래 스크린샷에서는 가상 머신을 열지만 리소스 상태 페이지에 모든 리소스 종류에 대한 세부 정보가 표시될 수 있습니다. 

특정 리소스에 대한 리소스 상태 페이지를 열려면 다음을 수행합니다.

1. [자산 인벤토리 페이지](asset-inventory.md)에서 리소스를 선택합니다.

    :::image type="content" source="media/investigate-resource-health/inventory-select-resource.png" alt-text="자산 인벤토리에서 특정 리소스를 선택하여 리소스 상태 페이지를 봅니다." lightbox="./media/investigate-resource-health/inventory-select-resource.png":::

1. 리소스 상태 페이지의 왼쪽 창을 사용하여 리소스의 구독, 상태 및 모니터링 정보에 대한 개요를 봅니다. 리소스에 대해 강화된 보안 기능을 사용할 수 있는지 여부도 확인할 수 있습니다.

    :::image type="content" source="media/investigate-resource-health/resource-health-left-pane.png" alt-text="Microsoft Defender for Cloud의 리소스 상태 페이지 왼쪽 창에는 리소스의 구독, 상태 및 모니터링 정보가 표시됩니다. 여기에는 미해결 보안 권장 사항 및 보안 경고의 총 수도 포함됩니다.":::

1. 오른쪽 창에 있는 두 개의 탭을 사용하여 이 리소스에 적용되는 보안 권장 사항 및 경고 목록을 검토합니다.

    :::image type="content" source="media/investigate-resource-health/resource-health-right-pane.png" alt-text="Microsoft Defender for Cloud의 리소스 상태 페이지 오른쪽 창에는 권장 사항 및 경고라는 두 개의 탭이 있습니다." lightbox="./media/investigate-resource-health/resource-health-right-pane.png":::

    > [!NOTE]
    > Microsoft Defender for Cloud는 "정상" 및 "비정상"이라는 용어를 사용하여 리소스의 보안 상태를 설명합니다. 이러한 용어는 리소스가 특정 [보안 권장 사항](security-policy-concept.md#what-is-a-security-recommendation)을 준수하는지 여부와 관련이 있습니다.
    >
    > 위의 스크린샷에서 이 리소스가 "정상"이더라도 권장 사항이 나열된 것을 볼 수 있습니다. 리소스 상태 페이지의 한 가지 이점은 모든 권장 사항이 나열되므로 리소스의 상태를 완벽하게 파악할 수 있다는 것입니다. 


## <a name="evaluate-the-outstanding-security-issues-for-a-resource"></a>리소스의 미해결 보안 문제 평가

리소스 상태 페이지에는 "비정상" 리소스에 대한 권장 사항 및 활성 경고가 나열됩니다. 

- 구독에 적용 되는 정책에 따라 리소스가 확정 되도록 하려면 권장 사항에 설명된 문제를 해결합니다.
    1. 오른쪽 창에서 권장 사항을 선택합니다.
    1. 화면의 지시에 따라 계속합니다.

        > [!TIP]
        > 발생한 문제를 보안 권장 사항에 따라 해결하는 지침은 Defender for Cloud의 각 권장 사항에 따라 다릅니다.
        >
        > 먼저 해결할 권장 사항을 결정하려면 각 권장 사항의 심각도와 [보안 점수에 미치는 잠재적 영향](secure-score-security-controls.md#security-controls-and-their-recommendations)을 살펴보면 됩니다.

- 보안 경고를 조사하려면 다음을 수행합니다.
    1. 오른쪽 창에서 경고를 선택합니다.
    1. [보안 경고에 대응](managing-and-responding-alerts.md#respond-to-security-alerts)의 지침을 따릅니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Defender for Cloud의 리소스 상태 페이지를 사용하는 방법을 알아보았습니다.

자세히 알아보려면 다음 관련 페이지를 참조하세요.

- [보안 경고에 대응](managing-and-responding-alerts.md#respond-to-security-alerts)
- [보안 권장 사항 검토](review-security-recommendations.md)
