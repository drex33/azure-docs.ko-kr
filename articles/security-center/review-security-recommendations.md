---
title: 클라우드용 Microsoft Defender의 보안 권장 사항
description: 이 문서에서는 Microsoft Defender for Cloud의 권장 사항이 Azure 리소스를 보호하고 보안 정책을 준수하는 데 도움이 되는 방법을 안내합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/07/2021
ms.author: memildin
ms.openlocfilehash: eb310e8edfb48c2252ae596aa95225e00329f5d2
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988241"
---
# <a name="review-your-security-recommendations"></a>보안 권장 사항 검토

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 항목에서는 Azure 리소스를 보호하는 데 도움이 되는 Microsoft Defender for Cloud의 권장 사항을 보고 이해하는 방법을 설명합니다.

## <a name="monitor-recommendations"></a>권장 사항 모니터링 <a name="monitor-recommendations"></a>

Defender for Cloud는 리소스의 보안 상태를 분석하여 잠재적인 취약성을 식별합니다. 

1. Defender for Cloud 메뉴에서 **권장 사항** 페이지를 열어 환경에 적용 가능한 권장 사항을 확인합니다. 권장 사항은 보안 컨트롤로 그룹화됩니다.

    :::image type="content" source="./media/review-security-recommendations/view-recommendations.png" alt-text="보안 컨트롤별로 그룹화된 권장 사항." lightbox="./media/review-security-recommendations/view-recommendations.png":::

1. 리소스 종류, 심각도, 환경 또는 중요한 기타 기준에 대한 권장 사항을 찾으려면 권장 사항 목록 위의 선택적 필터를 사용합니다.

    :::image type="content" source="media/review-security-recommendations/recommendation-list-filters.png" alt-text="클라우드용 Microsoft Defender 권장 사항 목록을 구체화하기 위한 필터입니다.":::

1. 컨트롤을 확장하고 특정 권장 사항을 선택하여 권장 사항 세부 정보 페이지를 표시합니다.

    :::image type="content" source="./media/review-security-recommendations/recommendation-details-page.png" alt-text="권장 사항 세부 정보 페이지" lightbox="./media/review-security-recommendations/recommendation-details-page.png":::

    페이지에는 다음 내용이 포함되어 있습니다.

    1. 지원되는 권장 사항의 경우 맨 위 도구 모음에 다음 단추 중 하나 이상이 표시됩니다.
        - **적용** 및 **거부(강제** [적용/거부 권장 사항을 통해 잘못된 구성 방지](prevent-misconfigurations.md)참조).
        - **정책 정의를 확인하여** 기본 정책에 대한 Azure Policy 항목으로 직접 이동합니다.
        - **쿼리 열기** - 모든 권장 사항에는 Azure Resource Graph Explorer를 사용하여 영향을 받는 리소스에 대한 자세한 정보를 볼 수 있는 옵션이 있습니다.
    1. **심각도 표시기 입니다.**
    1. **새로 고침 간격(관련** 있는 경우)
    1. 권장 사항에 대한 예외가 있는 경우 **제외된 리소스의 수이며,** 특정 리소스를 볼 수 있는 링크와 함께 제외된 리소스 수를 표시합니다.
    1. 권장 **사항에서 전술과 기술을 정의한 경우 MITRE ATT&CK ®** 기법에 매핑하려면 MITRE 사이트의 관련 페이지에 대한 링크 아이콘을 선택합니다. 

        :::image type="content" source="media/review-security-recommendations/tactics-window.png" alt-text="권장 사항에 대한 MITRE 전술 매핑의 스크린샷.":::

    1. **설명** - 보안 문제에 대한 간단한 설명입니다.
    1. 관련된 경우 세부 정보 페이지에는 관련 권장 사항 테이블도 **포함됩니다.**

        관계 유형은 다음과 같습니다.

        - **필수 조건** - 선택한 권장 사항 전에 완료해야 하는 권장 사항
        - **대안** - 선택한 권장 사항의 목표를 달성하는 다른 방법을 제공하는 다른 권장 사항
        - **종속** - 선택한 권장 사항이 필수 조건인 권장 사항

        각 관련 권장 사항에 대해 비정상 리소스 수가 "영향을 받는 리소스" 열에 표시됩니다.

        > [!TIP]
        > 관련 권장 사항이 회색으로 표시됩니다. 해당 종속성이 아직 완료되지 않았으므로 사용할 수 없습니다.

    1. **수정 단계** - 영향을 받는 리소스에 대한 보안 문제를 해결하는 데 필요한 수동 단계에 대한 설명입니다. **수정** 옵션**이 있는 권장 사항의 경우 제안된 수정 사항을 리소스에 적용하기 전에 **수정 논리 보기** 를 선택할 수 있습니다.

    1. **영향을 받는 리소스** - 리소스가 탭으로 그룹화됩니다.
        - **정상 리소스** – 영향을 받지 않거나 이미 문제를 해결한 관련 리소스입니다.
        - **비정상 리소스** – 여전히 식별된 문제의 영향을 받는 리소스입니다.
        - **해당 없음 리소스** – 권장 사항에서 명확한 답변을 제공할 수 없는 리소스입니다. 해당 없음 탭에는 각 리소스에 대한 이유도 포함되어 있습니다. 

            :::image type="content" source="./media/review-security-recommendations/recommendations-not-applicable-reasons.png" alt-text="이유가 포함된 해당 없음 리소스":::
    1. 권장 사항을 재구성하거나 논리 앱을 트리거하는 작업 단추입니다.

## <a name="review-recommendation-data-in-azure-resource-graph-explorer-arg"></a>ARG(Azure Resource Graph Explorer)에서 권장 사항 데이터 살펴보기

권장 사항 세부 정보 페이지의 도구 모음에는 [ARG(Azure Resource Graph)의](../governance/resource-graph/index.yml)세부 정보를 탐색하는 **쿼리 열기** 단추가 포함되어 있습니다. 이 단추는 여러 구독에서 클라우드용 Defender의 보안 태세 데이터를 쿼리하는 기능을 제공하는 Azure 서비스입니다.

ARG는 강력한 필터링, 그룹화, 정렬 기능을 통해 클라우드 환경에서 대규모로 쿼리하는 기능을 사용하여 효율적인 리소스 탐색을 제공하도록 설계되었습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다.

[KQL(Kusto 쿼리 언어)을](/azure/data-explorer/kusto/query/)사용하여 Defender for Cloud 데이터를 다른 리소스 속성과 상호 참조할 수 있습니다.

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

이 문서에서는 Defender for Cloud의 보안 권장 사항을 소개했습니다. 관련 정보는 다음과 같습니다.

- [권장 사항 교정](implement-security-recommendations.md) - Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
- [적용/거부 권장 사항을 사용하여 구성 오류를 방지합니다](prevent-misconfigurations.md).
- [Defender for Cloud 트리거에 대한 응답 자동화](workflow-automation.md)--권장 사항에 대한 응답 자동화
- [권장 사항에서 리소스 제외](exempt-resource.md)
- [보안 권장 사항 - 참조 가이드](recommendations-reference.md)
