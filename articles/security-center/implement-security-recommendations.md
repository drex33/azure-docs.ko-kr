---
title: Microsoft Defender for Cloud에서 보안 권장 사항 구현 | Microsoft Docs
description: 이 문서에서는 Microsoft Defender for Cloud에서 리소스를 보호 하 고 보안 정책을 충족 하기 위해 권장 사항에 응답 하는 방법을 설명 합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: d01a8f806154cdf0260f0dcf8c9f6c65386c99f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437919"
---
# <a name="implement-security-recommendations-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud에서 보안 권장 사항 구현

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

권장 사항은 리소스의 보안을 강화하는 방법에 대한 제안을 제공합니다. 권장 사항에서 제공하는 수정 단계를 수행하여 권장 사항을 구현할 수 있습니다.

## <a name="remediation-steps"></a>수정 단계 <a name="remediation-steps"></a>

모든 권장 사항을 검토한 후 가장 먼저 수정할 권장 사항을 결정합니다. 보안 점수를 늘릴 가능성이 가장 높은 보안 제어의 우선 순위를 지정하는 것이 좋습니다.

1. 목록에서 권장 사항을 선택합니다.

1. **수정 단계** 섹션의 지침을 따릅니다. 각 권장 사항에는 고유한 지침이 있습니다. 다음 스크린샷에서는 HTTPS를 통한 트래픽만 허용하도록 애플리케이션을 구성하는 수정 단계를 보여 줍니다.

    :::image type="content" source="./media/implement-security-recommendations/security-center-remediate-recommendation.png" alt-text="권장 사항에 대한 수동 수정 단계." lightbox="./media/implement-security-recommendations/security-center-remediate-recommendation.png":::

1. 완료되면 문제가 해결되었는지 알려 주는 알림이 표시됩니다.

## <a name="fix-button"></a>수정 단추

수정을 간소화하고 환경 보안을 개선하고 보안 점수를 늘리기 위해 많은 권장 사항에 **수정** 옵션이 포함되어 있습니다.

**수정** 을 통해 여러 리소스에 대한 권장 사항을 빠르게 수정할 수 있습니다.

> [!TIP]
> **수정** 기능은 특정 권장 사항에만 사용할 수 있습니다. 사용 가능한 수정이 있는 권장 사항을 찾으려면 권장 사항 목록에 대해 **응답 작업** 필터를 사용합니다.
> 
> :::image type="content" source="media/implement-security-recommendations/quick-fix-filter.png" alt-text="권장 사항 목록 위의 필터를 사용하여 수정 옵션이 있는 권장 사항을 찾습니다.":::

**수정** 을 구현하려면:

1. **수정** 작업 아이콘 :::image type="icon" source="media/implement-security-recommendations/fix-icon.png" border="false":::이 있는 권장 사항 목록에서 권장 사항을 선택합니다.

    :::image type="content" source="./media/implement-security-recommendations/security-center-recommendations-fix-action.png" alt-text="수정 작업이 있는 권장 사항이 강조 표시된 권장 사항 목록" lightbox="./media/implement-security-recommendations/security-center-recommendations-fix-action.png":::

1. **비정상 리소스** 탭에서 권장 사항을 구현하려는 리소스를 선택하고 **수정** 을 선택합니다.

    > [!NOTE]
    > 나열된 리소스 중 일부는 수정할 수 있는 적절한 권한이 없기 때문에 사용하지 못할 수 있습니다.

1. 확인 상자에서 수정 세부 정보 및 영향을 확인합니다.

    ![빠른 수정.](./media/implement-security-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > **수정** 을 클릭하면 열리는 **리소스 수정** 창에서 회색 상자에 영향이 나열됩니다. 영향에는 **수정** 작업을 진행할 때 발생하는 변경 내용이 나열됩니다.

1. 필요한 경우 관련 매개 변수를 삽입하고 수정을 승인합니다.

    > [!NOTE]
    > 수정 완료 후 **정상 리소스** 탭에서 리소스를 확인하는 데 몇 분 정도 걸릴 수 있습니다. 수정 작업을 보려면 [활동 로그](#activity-log)를 확인합니다.

1. 완료되면 수정에 성공했는지 알려 주는 알림이 표시됩니다.

## <a name="fix-actions-logged-to-the-activity-log"></a>활동 로그에 기록되는 수정 작업 <a name="activity-log"></a>

수정 작업은 템플릿 배포 또는 REST API 요청을 사용 하 여 `PATCH` 리소스에 대 한 구성을 적용 합니다. 이러한 작업은 [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)에 기록됩니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 클라우드에 대 한 Defender의 권장 사항을 수정 하는 방법을 살펴보았습니다. 사용자 환경에 대 한 권장 사항을 정의 하 고 선택 하는 방법을 알아보려면 다음 페이지를 참조 하세요.

- [보안 정책, 이니셔티브 및 권장 사항은 무엇인가요?](security-policy-concept.md)