---
title: Microsoft Defender for Cloud의 보안 권장 사항
description: 이 문서에서는 Azure 리소스를 보호 하 고 보안 정책을 준수 하는 데 도움이 되는 Microsoft Defender for Cloud의 권장 사항에 대해 안내 합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 01a89b52efb3b0184001dd130499e0438687772f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103650"
---
# <a name="review-your-security-recommendations"></a>보안 권장 사항 검토

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 항목에서는 Azure 리소스를 보호 하는 데 도움이 되는 Microsoft Defender for Cloud의 권장 사항을 보고 이해 하는 방법에 대해 설명 합니다.

## <a name="monitor-recommendations"></a>권장 사항 모니터링 <a name="monitor-recommendations"></a>

Defender for Cloud는 리소스의 보안 상태를 분석 하 여 잠재적인 취약성을 식별 합니다. 

1. 클라우드에 대 한 Defender의 메뉴에서 **권장 사항** 페이지를 열어 사용자 환경에 적용할 수 있는 권장 사항을 확인 합니다. 권장 사항은 보안 컨트롤로 그룹화됩니다.

    :::image type="content" source="./media/review-security-recommendations/view-recommendations.png" alt-text="보안 컨트롤별로 그룹화된 권장 사항." lightbox="./media/review-security-recommendations/view-recommendations.png":::

1. 리소스 종류, 심각도, 환경 또는 중요한 기타 기준에 대한 권장 사항을 찾으려면 권장 사항 목록 위의 선택적 필터를 사용합니다.

    :::image type="content" source="media/review-security-recommendations/recommendation-list-filters.png" alt-text="클라우드 권장 사항에 대 한 Microsoft Defender 목록을 구체화 하는 필터입니다.":::

1. 컨트롤을 확장하고 특정 권장 사항을 선택하여 권장 사항 세부 정보 페이지를 표시합니다.

    :::image type="content" source="./media/review-security-recommendations/recommendation-details-page.png" alt-text="권장 사항 세부 정보 페이지" lightbox="./media/review-security-recommendations/recommendation-details-page.png":::

    페이지에는 다음 내용이 포함되어 있습니다.

    1. 지원되는 권장 사항의 경우 맨 위 도구 모음에 다음 단추 중 하나 이상이 표시됩니다.
        - **적용** 및 **거부** ( [강제 적용/거부 권장 구성에서 잘못 된 구성 방지](prevent-misconfigurations.md)참조).
        - 기본 정책에 대 한 Azure Policy 항목으로 직접 이동 하는 **정책 정의를 확인** 합니다.
        - **쿼리 열기** -모든 권장 사항에는 Azure 리소스 Graph 탐색기를 사용 하 여 영향을 받는 리소스에 대 한 자세한 정보를 볼 수 있는 옵션이 있습니다.
    1. **심각도 표시기**.
    1. **새로 고침 간격** (해당 하는 경우).
    1. **제외 된 리소스 수** 이 권장 구성에 대 한 예외가 있는 경우 제외 된 리소스 수를 표시 합니다.
    1. **설명** -보안 문제에 대 한 간단한 설명입니다.
    1. 관련성이 있는 경우 세부 정보 페이지에는 **관련 권장 사항** 테이블도 포함 되어 있습니다.

        관계 유형은 다음과 같습니다.

        - **필수 조건** -선택한 권장 사항 보다 먼저 완료 해야 하는 권장 사항
        - **대안** -선택한 권장 사항의 목표를 달성 하는 다른 방법을 제공 하는 다른 권장 사항
        - **종속** -선택한 권장 사항이 필수 구성 요소인 권장 사항

        관련 된 각 권장 사항에 대해 비정상 리소스의 수는 "영향을 받는 리소스" 열에 표시 됩니다.

        > [!TIP]
        > 관련 권장 사항이 회색으로 표시 되 면 해당 종속성은 아직 완료 되지 않았으므로 사용할 수 없습니다.

    1. **수정 단계** - 영향을 받는 리소스에 대한 보안 문제를 해결하는 데 필요한 수동 단계에 대한 설명입니다. **수정** 옵션**이 있는 권장 사항의 경우 제안된 수정 사항을 리소스에 적용하기 전에 **수정 논리 보기** 를 선택할 수 있습니다.

    1. **영향을 받는 리소스** - 리소스가 탭으로 그룹화됩니다.
        - **정상 리소스** – 영향을 받지 않거나 이미 문제를 해결한 관련 리소스입니다.
        - **비정상 리소스** – 여전히 식별된 문제의 영향을 받는 리소스입니다.
        - **제거 됨** -평가 주기 사이에 삭제 된 리소스입니다 (이는 새로 고침 간격으로 정의 됨).
        - **해당 없음 리소스** – 권장 사항에서 명확한 답변을 제공할 수 없는 리소스입니다. 해당 없음 탭에는 각 리소스에 대한 이유도 포함되어 있습니다. 

            :::image type="content" source="./media/review-security-recommendations/recommendations-not-applicable-reasons.png" alt-text="이유가 포함된 해당 없음 리소스":::
    1. 권장 사항을 재구성하거나 논리 앱을 트리거하는 작업 단추입니다.

## <a name="review-recommendation-data-in-azure-resource-graph-explorer-arg"></a>ARG(Azure Resource Graph Explorer)에서 권장 사항 데이터 살펴보기

권장 사항 세부 정보 페이지의 도구 모음에는 [azure 리소스 Graph (ARG)](../governance/resource-graph/index.yml)의 세부 정보를 탐색 하는 **쿼리 열기** 단추가 포함 되어 있습니다 .이를 통해 클라우드 보안 상태 데이터에 대해 여러 구독을 쿼리 하는 기능을 제공 하는 azure 서비스입니다.

ARG는 강력한 필터링, 그룹화, 정렬 기능을 통해 클라우드 환경에서 대규모로 쿼리하는 기능을 사용하여 효율적인 리소스 탐색을 제공하도록 설계되었습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다.

[Kusto 쿼리 언어 (KQL)](/azure/data-explorer/kusto/query/)를 사용 하 여 다른 리소스 속성을 사용 하 여 클라우드 데이터의 Defender를 상호 참조할 수 있습니다.

예를 들어, 이 권장 사항 세부 정보 페이지에는 영향을 받는 15개의 리소스가 표시됩니다.

:::image type="content" source="./media/review-security-recommendations/open-query.png" alt-text="권장 사항 세부 정보 페이지의 **쿼리 열기** 단추.":::

기본 쿼리를 열고 실행하는 경우, Azure Resource Graph Explorer는 이 권장 사항에 대해 동일한 15개의 리소스와 해당 상태를 반환합니다. 

:::image type="content" source="./media/review-security-recommendations/run-query.png" alt-text="이전 스크린샷에 표시된 권장 사항에 대한 결과를 보여 주는 Azure Resource Graph Explorer.":::

## <a name="preview-recommendations"></a>미리 보기 권장 사항

**미리 보기** 로 플래그가 지정된 권장 사항은 보안 점수 계산에 포함되지 않습니다.

미리 보기 기간이 종료되면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정해야 합니다.

미리 보기 추천 사항의 예는 다음과 같습니다.

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="미리 보기 플래그가 있는 권장 사항.":::
 
## <a name="next-steps"></a>다음 단계

이 문서에서는 Defender for Cloud의 보안 권장 사항에 대해 소개 했습니다. 관련 정보는 다음과 같습니다.

- [권장 사항 교정](implement-security-recommendations.md) - Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
- [적용/거부 권장 사항을 사용하여 구성 오류를 방지합니다](prevent-misconfigurations.md).
- [클라우드 트리거에 대 한 Defender에 대 한 응답 자동화](workflow-automation.md)--권장 사항에 대 한 응답 자동화
- [권장 사항에서 리소스 제외](exempt-resource.md)
- [보안 권장 사항 - 참조 가이드](recommendations-reference.md)
