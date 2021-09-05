---
title: Azure Security Center의 권장 사항 교정 | Microsoft Docs
description: 이 문서에서는 Azure Security Center의 권장 사항에 응답하여 리소스를 보호하고 보안 정책을 충족하는 방법을 설명합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f0caaea2296a8981df4717497cb641676b43dea1
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237078"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center의 권장 사항 교정

권장 사항은 리소스의 보안을 강화하는 방법에 대한 제안을 제공합니다. 권장 사항에서 제공하는 수정 단계를 수행하여 권장 사항을 구현할 수 있습니다.

## <a name="remediation-steps"></a>수정 단계 <a name="remediation-steps"></a>

모든 권장 사항을 검토한 후 가장 먼저 수정할 권장 사항을 결정합니다. 보안 점수를 늘릴 가능성이 가장 높은 보안 제어의 우선 순위를 지정하는 것이 좋습니다.

1. 목록에서 권장 사항을 선택합니다.

1. **수정 단계** 섹션의 지침을 따릅니다. 각 권장 사항에는 고유한 지침이 있습니다. 다음 스크린샷에서는 HTTPS를 통한 트래픽만 허용하도록 애플리케이션을 구성하는 수정 단계를 보여 줍니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="권장 사항에 대한 수동 수정 단계." lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. 완료되면 문제가 해결되었는지 알려 주는 알림이 표시됩니다.

## <a name="fix-button"></a>수정 단추

수정을 간소화하고 환경 보안을 개선하고 보안 점수를 늘리기 위해 많은 권장 사항에 **수정** 옵션이 포함되어 있습니다.

**수정** 을 통해 여러 리소스에 대한 권장 사항을 빠르게 수정할 수 있습니다.

> [!TIP]
> **수정** 기능은 특정 권장 사항에만 사용할 수 있습니다. 사용 가능한 수정이 있는 권장 사항을 찾으려면 권장 사항 목록에 대해 **응답 작업** 필터를 사용합니다.
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="권장 사항 목록 위의 필터를 사용하여 수정 옵션이 있는 권장 사항을 찾습니다.":::

**수정** 을 구현하려면:

1. **수정** 작업 아이콘 :::image type="icon" source="media/security-center-remediate-recommendations/fix-icon.png" border="false":::이 있는 권장 사항 목록에서 권장 사항을 선택합니다.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-recommendations-fix-action.png" alt-text="수정 작업이 있는 권장 사항이 강조 표시된 권장 사항 목록" lightbox="./media/security-center-remediate-recommendations/security-center-recommendations-fix-action.png#lightbox":::

1. **비정상 리소스** 탭에서 권장 사항을 구현하려는 리소스를 선택하고 **수정** 을 선택합니다.

    > [!NOTE]
    > 나열된 리소스 중 일부는 수정할 수 있는 적절한 권한이 없기 때문에 사용하지 못할 수 있습니다.

1. 확인 상자에서 수정 세부 정보 및 영향을 확인합니다.

    ![빠른 수정.](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > **수정** 을 클릭하면 열리는 **리소스 수정** 창에서 회색 상자에 영향이 나열됩니다. 영향에는 **수정** 작업을 진행할 때 발생하는 변경 내용이 나열됩니다.

1. 필요한 경우 관련 매개 변수를 삽입하고 수정을 승인합니다.

    > [!NOTE]
    > 수정 완료 후 **정상 리소스** 탭에서 리소스를 확인하는 데 몇 분 정도 걸릴 수 있습니다. 수정 작업을 보려면 [활동 로그](#activity-log)를 확인합니다.

1. 완료되면 수정에 성공했는지 알려 주는 알림이 표시됩니다.

## <a name="fix-actions-logged-to-the-activity-log"></a>활동 로그에 기록되는 수정 작업 <a name="activity-log"></a>

수정 작업은 템플릿 배포 또는 REST PATCH API 호출을 사용하여 리소스의 구성을 적용합니다. 이러한 작업은 [Azure 활동 로그](../azure-resource-manager/management/view-activity-logs.md)에 기록됩니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center의 권장 사항을 수정하는 방법을 소개했습니다. Security Center에 대한 자세한 내용은 다음 페이지를 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md): Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [보안 정책, 이니셔티브 및 권장 사항은 무엇인가요?](security-policy-concept.md)