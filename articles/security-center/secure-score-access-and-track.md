---
title: Microsoft Defender for Cloud에서 보안 점수 추적
description: Microsoft Defender for Cloud에서 보안 점수를 액세스 하 고 추적 하는 여러 가지 방법에 대해 알아보세요.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 61cd7dd97f0b563d5bcb1030830edfa073ab0571
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452835"
---
# <a name="access-and-track-your-secure-score"></a>보안 점수 액세스 및 추적

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

다음 섹션에서 설명하는 것과 같이 Azure Portal을 통해 또는 프로그래밍 방식으로 전체 보안 점수와 구독당 점수를 확인할 수 있습니다.

> [!TIP]
> 점수를 계산하는 방법에 대한 자세한 설명은 [계산 - 점수 이해](secure-score-security-controls.md#calculations---understanding-your-score)를 참조하세요.

## <a name="get-your-secure-score-from-the-portal"></a>포털에서 보안 점수 가져오기

클라우드에 대 한 defender는 포털에서 점수를 명확 하 게 표시 합니다. 클라우드 개요 페이지의 첫 번째 주 타일입니다. 이 타일을 선택하면 전용 보안 점수 페이지로 이동하고, 구독별로 분석된 점수를 볼 수 있습니다. 단일 구독을 선택하여 우선 순위가 지정된 권장 사항의 세부 목록과 이러한 권장 사항에 따라 수정할 때 구독의 점수에 미치는 영향을 확인할 수 있습니다. 

이에 대 한 자세한 내용은 클라우드의 포털 페이지에 대 한 Defender의 다음 위치에서 보안 점수를 표시 합니다.

- 클라우드 **개요** (주 대시보드)의 경우 Defender의 타일에서:

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="클라우드의 대시보드에 대 한 Defender의 보안 점수":::

- 전용 **보안 점수** 페이지에서 구독 및 관리 그룹에 대한 보안 점수를 볼 수 있습니다.

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="클라우드의 보안 점수에 대 한 Defender의 구독 보안 점수 페이지":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="클라우드의 보안 점수에 대 한 Defender의 관리 그룹에 대 한 보안 점수 페이지":::

    > [!NOTE]
    > 충분한 권한이 없는 모든 관리 그룹에는 해당 점수가 "제한됨"으로 표시됩니다. 

- **권장 사항** 페이지 상단:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="클라우드의 권장 사항 페이지에 대 한 보안 점수":::

## <a name="get-your-secure-score-from-the-rest-api"></a>REST API에서 보안 점수 가져오기

보안 점수 API를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공하고, 시간 경과에 따른 보안 점수에 대한 사용자 고유의 보고 메커니즘을 빌드합니다. 예를 들어 [보안 점수](/rest/api/securitycenter/securescores) API를 사용하여 특정 구독에 대한 점수를 가져올 수 있습니다. 추가로 [보안 점수 제어 API](/rest/api/securitycenter/securescorecontrols)를 사용하여 보안 제어 및 구독의 현재 점수를 나열할 수 있습니다.

![API를 통해 단일 보안 점수 검색.](media/secure-score-security-controls/single-secure-score-via-api.png)

보안 점수 API 기반의 도구 예는 [GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)을 참조하세요. 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Azure Resource Graph에서 보안 점수 가져오기

Azure Resource Graph의 강력한 필터링, 그룹화 및 정렬 기능을 통해 클라우드 환경에서 리소스 정보에 즉시 액세스할 수 있습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다. [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).

Azure Resource Graph를 사용하여 여러 구독의 보안 점수에 액세스하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Resource Graph Explorer** 를 엽니다.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph Explorer 시작** 권장 사항 페이지" :::

1. Kusto 쿼리를 입력합니다(지침은 아래 예 사용).

    - 이 쿼리는 구독 ID, 현재 점수(포인트 및 백분율), 구독의 최대 점수를 반환합니다. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - 이 쿼리는 모든 보안 제어의 상태를 반환합니다. 각 제어에 대해 비정상 리소스 수, 현재 점수 및 최대 점수를 가져옵니다. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. **쿼리 실행** 을 선택합니다.


## <a name="tracking-your-secure-score-over-time"></a>시간 경과에 따른 보안 점수 추적

### <a name="secure-score-over-time-report-in-workbooks-page"></a>통합 문서 페이지의 시간에 따른 보안 점수 보고서

Defender for Cloud의 통합 페이지에는 구독, 보안 제어 등의 점수를 시각적으로 추적 하기 위한 미리 만들어진 보고서가 포함 되어 있습니다. [클라우드 데이터를 위한 Defender의 풍부한 대화형 보고서 만들기](custom-dashboards-azure-workbooks.md)에서 자세히 알아보세요.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="클라우드 통합 문서 갤러리에 대 한 Microsoft Defender의 시간에 따른 보안 점수 보고서의 섹션":::

### <a name="power-bi-pro-dashboards"></a>Power BI Pro 대시보드

Pro 계정이 있는 Power BI 사용자인 경우 **시간 경과에 따른 보안 점수** Power BI 대시보드를 사용하여 시간 경과에 따른 보안 점수를 추적하고 변화를 조사할 수 있습니다.

> [!TIP]
> GitHub에서 클라우드 용 Microsoft Defender 커뮤니티의 전용 영역에서 보안 점수를 사용 하 여 프로그래밍 방식으로 작업 하는 데 사용할 수 있는 다른 도구 뿐만 아니라이 대시보드를 찾을 수 있습니다.https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

대시보드에는 보안 상태를 분석하는 데 도움이 되는 다음 두 보고서가 포함되어 있습니다.

- **리소스 요약** - 리소스 상태와 관련하여 요약된 데이터를 제공합니다.
- **보안 점수 요약** - 점수 진행 상황과 관련하여 요약된 데이터를 제공합니다. "구독당 시간 경과에 따른 보안 점수" 차트를 사용하여 점수의 변화를 확인할 수 있습니다. 점수의 변화가 큰 경우 "보안 점수에 영향을 줄 수 있는 감지된 변화" 표에서 변화의 원인이 될 수 있는 사항을 확인합니다. 이 표에서는 삭제된 리소스, 새로 배포된 리소스, 권장 사항 중 하나에 대해 보안 상태가 변경된 리소스가 표시됩니다.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="시간 경과에 따른 보안 점수를 추적하고 변화를 조사하기 위한 시간 경과에 따른 보안 점수 Power BI 대시보드 옵션.":::


## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 점수에 액세스하고 추적하는 방법을 설명했습니다. 관련 자료는 다음 문서를 참조하세요.

- [권장 사항의 다양한 요소에 대해 알아보기](review-security-recommendations.md)
- [권장 사항을 수정하는 방법 알아보기](implement-security-recommendations.md)
- [보안 점수를 사용하여 프로그래밍 방식으로 작업하기 위한 GitHub 기반 도구 보기](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)
