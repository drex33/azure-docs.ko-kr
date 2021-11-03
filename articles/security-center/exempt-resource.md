---
title: 리소스, 구독, 관리 그룹 및 보안 점수에서 클라우드 권장 사항에 대 한 Microsoft Defender 제외
description: 구독 또는 관리 그룹에서 보안 권장 사항을 제외하 고 보안 점수에 영향을 주지 않도록 규칙을 만드는 방법을 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 05/12/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9ceda40ee7a879b2f15e77291dab7212f9e3a7a7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463680"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>보안 점수에서 리소스 및 권장 사항 제외 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

모든 보안 팀의 핵심 우선 순위는 분석가가 조직에 중요한 작업과 인시던트에 집중할 수 있도록 하는 것입니다. Defender for Cloud에는 환경을 사용자 지정 하 고 보안 점수가 조직의 보안 우선 순위를 반영 하도록 하는 많은 기능이 있습니다. **제외** 옵션은 이러한 기능 중 하나입니다.

Microsoft Defender for Cloud에서 보안 권장 사항을 조사할 때 가장 먼저 검토 하는 정보 중 하나는 영향을 받는 리소스 목록입니다.

때때로 포함하면 안 되는 것으로 생각되는 리소스가 나열됩니다. 또는 해당 영역이 아닌 것으로 생각되는 범위에 권장 사항이 표시됩니다. 리소스가 Defender for Cloud에서 추적 되지 않는 프로세스에 의해 재구성 되었을 수 있습니다. 권장 사항은 특정 구독에 적합하지 않을 수도 있습니다. 또는 조직이 특정 리소스 또는 권장 사항과 관련된 위험을 감수하기로 결정했을 수도 있습니다.

이러한 경우 다음과 같이 권장 사항에 대한 예외를 만들 수 있습니다.

- **리소스를 제외** 하여 향후 비정상적인 리소스에 나열되지 않고 보안 점수에 영향을 주지 않도록 합니다. 리소스가 해당되지 않는 것으로 나열되고 선택한 특정 근거와 함께 이유가 "예외"로 표시됩니다.

- 권장 사항이 보안 점수에 영향을 주지 않고 향후 구독 또는 관리 그룹에 표시되지 않도록 하려면 **구독 또는 관리 그룹을 제외** 합니다. 이는 기존 리소스와 나중에 만드는 리소스와 관련이 있습니다. 권장 사항은 선택한 범위에 대해 선택한 특정 근거로 표시됩니다.

## <a name="availability"></a>가용성

| 양상                          | 세부 정보                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:                  | 미리 보기<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| 가격 책정:                        | 클라우드의 향상 된 보안 기능을 사용할 수 있도록 Microsoft Defender를 사용 하는 고객에 게 추가 비용 없이 제공 되는 프리미엄 Azure Policy 기능입니다. 다른 사용자에게는 나중에 요금이 부과될 수 있습니다.                                                                                                                                                                 |
| 필요한 역할 및 권한: | 예외를 만들기 위한 **소유자** 또는 **리소스 정책 기여자**<br>규칙을 만들려면 Azure Policy에서 정책을 편집할 수 있는 권한이 필요합니다.<br>[Azure Policy에서 Azure RBAC 사용 권한](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)에 대해 자세히 알아보세요.                                            |
| 제한 사항:                    | 클라우드의 기본 이니셔티브, [Azure 보안 벤치 마크](/security/benchmark/azure/introduction)또는 제공 된 규제 표준 이니셔티브에 대해 Defender에 포함 된 권장 사항에 대해서만 예외를 만들 수 있습니다. 사용자 지정 이니셔티브에서 생성된 권장 사항은 제외될 수 없습니다. [정책, 이니셔티브 및 권장 사항](security-policy-concept.md) 간의 관계에 대해 자세히 알아보세요. |
| 클라우드:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>예외 정의

Defender for Cloud가 구독, 관리 그룹 또는 리소스에 대해 수행 하는 보안 권장 사항을 미세 조정 하려면 다음에 대 한 예외 규칙을 만들 수 있습니다.

- 특정 **권장 사항** 을 표시하거나 "완화" 또는 "위험 수락"으로 표시합니다. 구독, 여러 구독 또는 전체 관리 그룹에 대한 권장 사항 예외를 만들 수 있습니다.
- 특정 권장 사항에 대해 **하나 이상의 리소스** 를 "완화" 또는 "위험 수락"으로 표시합니다.

> [!NOTE]
> 클라우드의 기본 이니셔티브, Azure 보안 벤치 마크 또는 제공 된 규제 표준 이니셔티브에 대해 Defender에 포함 된 권장 사항에 대해서만 예외를 만들 수 있습니다. 구독에 할당된 사용자 지정 이니셔티브에서 생성된 권장 사항은 제외될 수 없습니다. [정책, 이니셔티브 및 권장 사항](security-policy-concept.md) 간의 관계에 대해 자세히 알아보세요.

> [!TIP]
> API를 사용하여 예외를 만들 수도 있습니다. 예제 JSON의 경우 관련 구조에 대한 설명은 [Azure Policy 예외 구조](../governance/policy/concepts/exemption-structure.md)를 참조하세요.

예외 규칙을 만들려면 다음을 수행합니다.

1. 특정 권장 사항에 대한 권장 사항 세부 정보 페이지를 엽니다.

1. 페이지 상단의 도구 모음에서 **예외** 를 선택합니다.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="구독이나 관리 그룹에서 제외되는 권장 사항에 대한 예외 규칙을 만듭니다.":::

1. **예외** 창에서 다음을 수행합니다.
    1. 이 예외 규칙의 범위를 선택합니다.
        - 관리 그룹을 선택하는 경우 해당 그룹 내의 모든 구독에서 권장 사항이 제외됩니다.
        - 권장 사항에서 리소스를 하나 이상 제외하기 위해 이 규칙을 만드는 경우 "선택된 리소스"를 선택하고 목록에서 관련 항목을 선택합니다.

    1. 이 예외 규칙의 이름을 입력합니다.
    1. 필요에 따라 만료 날짜를 설정합니다.
    1. 예외에 대한 범주를 선택합니다.
        - 타사를 **통해 해결 됨** – 타사 서비스를 사용 하는 경우 Defender for Cloud가 식별 되지 않습니다. 

            > [!NOTE]
            > 권장 사항을 완화된 항목으로 제외하면 보안 점수에 대한 점수가 부여되지 않습니다. 그러나 비정상적인 리소스에 대해 점수가 *제거* 되지 않기 때문에 결과적으로는 점수가 증가합니다.

        - **위험 수락(면제)** – 이 권장 사항을 완화하지 않는 위험을 수락하기로 결정한 경우입니다.
    1. 선택적으로 설명을 입력합니다.
    1. **만들기** 를 선택합니다.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="구독 또는 관리 그룹에서 권장 사항을 제외하는 예외 규칙을 만드는 단계.":::

    예외가 적용되는 경우(최대 30분이 걸릴 수 있음):
    - 권장 사항 또는 리소스는 보안 점수에 영향을 주지 않습니다.
    - 특정 리소스를 제외하면 권장 사항 세부 정보 페이지의 **해당 없음** 탭에 나열됩니다.
    - 권장 사항을 제외 하 고 나면 기본적으로 클라우드의 권장 사항 페이지에 대해 Defender에서 숨겨집니다. 해당 페이지에서 **권장 사항 상태** 필터의 기본 옵션이 **해당 없는** 권장 사항을 제외하기 때문입니다. 보안 제어에서 모든 권장 사항을 제외하는 경우에도 마찬가지입니다.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Microsoft Defender for Cloud의 권장 사항 페이지에서 기본 필터 적용 되지 않는 권장 사항 및 보안 제어를 숨깁니다.":::

    - 권장 사항 세부 정보 페이지의 상단에 있는 정보 스트립은 제외된 리소스 수를 업데이트합니다.
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="제외된 리소스 수.":::

1. 제외된 리소스를 검토하려면 **해당 없음** 탭을 엽니다.

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="예외 수정.":::

    각 예외에 대한 이유는 테이블 (1)에 포함됩니다.

    예외를 수정하거나 삭제하려면 (2)와 같이 줄임표 메뉴("...")를 선택합니다.

1. 구독에 대 한 모든 예외 규칙을 검토하려면 정보 스트립에서 **예외 보기** 를 선택합니다.

    > [!IMPORTANT]
    > 하나의 권장 사항과 관련된 특정 예외를 확인하려면 관련 범위 및 권장 사항 이름에 따라 목록을 필터링하세요.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy의 예외 페이지":::

    > [!TIP]
    > 또는 [를 사용하여 예외에 대한 권장 사항을 찾습니다](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>구독에서 생성된 예외 모니터링

이 페이지의 앞부분에서 설명한 대로 예외 규칙은 구독 및 관리 그룹에서 리소스에 영향을 미치는 권장 사항을 세부적으로 제어하는 강력한 도구입니다. 

사용자가 이 기능을 어떻게 사용하고 있는지 추적하기 위해 예외가 생성되면 알리도록 논리 앱 플레이 북 및 필요한 모든 API 연결을 배포하는 ARM(Azure Resource Manager) 템플릿을 만들었습니다.

- 플레이 북에 대 한 자세한 내용은 tech community 블로그 게시물 [Microsoft Defender For Cloud에서 리소스 예외를 추적 하는 방법을](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580) 참조 하세요.
- [Microsoft Defender for Cloud GitHub 리포지토리](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption) 에서 ARM 템플릿을 찾을 수 있습니다.
- 모든 필수 구성 요소를 배포하려면 [자동화된 이 프로세스를 사용](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)합니다.

## <a name="use-the-inventory-to-find-resources-that-have-exemptions-applied"></a>인벤토리를 사용하여 예외가 적용된 리소스 찾기

Microsoft Defender for Cloud의 asset inventory 페이지는 클라우드 용 Defender에 연결 된 리소스의 보안 상태를 볼 수 있는 단일 페이지를 제공 합니다. [자산 인벤토리로 리소스 탐색 및 관리](asset-inventory.md)에서 자세히 알아보세요.

인벤토리 페이지에는 리소스 목록을 지정된 시나리오에서 가장 관심 있는 항목으로 좁힐 수 있는 여러 필터가 포함되어 있습니다. 이러한 필터 중 하나가 **예외 포함** 입니다. 이 필터를 사용하여 하나 이상의 권장 사항에서 제외된 모든 리소스를 찾습니다.

:::image type="content" source="media/exempt-resource/inventory-filter-exemptions.png" alt-text="클라우드의 자산 재고 페이지 및 예외를 포함 하는 리소스를 찾기 위한 필터":::


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Azure Resource Graph를 사용하여 예외에 대한 권장 사항 찾기

ARG(Azure Resource Graph)의 강력한 필터링, 그룹화 및 정렬 기능을 통해 클라우드 환경에서 리소스 정보에 즉시 액세스할 수 있습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다.

예외 규칙이 있는 모든 권장 사항을 보려면 다음을 수행합니다.

1. **Azure Resource Graph Explorer** 를 엽니다.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph Explorer 시작** 권장 사항 페이지" :::

1. 다음 쿼리를 입력하고 **쿼리 실행** 을 선택합니다.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


다음 페이지에서 자세히 알아보세요.
- [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).
- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language(KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>FAQ - 예외 규칙

- [하나의 권장 사항이 여러 정책 이니셔티브에 있으면 어떻게 되나요?](#what-happens-when-one-recommendation-is-in-multiple-policy-initiatives)
- [예외를 지원하지 않는 권장 사항이 있나요?](#are-there-any-recommendations-that-dont-support-exemption)

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>하나의 권장 사항이 여러 정책 이니셔티브에 있으면 어떻게 되나요?

보안 권장 사항이 둘 이상의 정책 이니셔티브에 표시되는 경우도 있습니다. 같은 구독에 할당된 동일한 권장 사항의 여러 인스턴스가 있고 권장 사항에 대한 예외를 만드는 경우 편집 권한이 있는 모든 이니셔티브에 영향을 줍니다. 

예를 들어 권장 사항 * * * *은 Microsoft Defender for Cloud의 모든 Azure 구독에 할당 된 기본 정책 이니셔티브의 일부입니다. XXXXX에도 있습니다.

이 권장 사항에 대한 예외를 만들면 다음 두 메시지 중 하나가 표시됩니다.

- 두 이니셔티브를 편집 하는 데 필요한 권한이 **있는** 경우 다음을 확인할 수 있습니다.

    *이 권장 사항은 [쉼표로 구분된 이니셔티브 이름]과 같은 여러 정책 이니셔티브에 포함되어 있습니다. 모든 항목에 대해 예외가 생성됩니다.*  

- 두 이니셔티브에 대해 충분 한 권한이 **없는** 경우이 메시지가 대신 표시 됩니다.

    *모든 정책 이니셔티브에 예외를 적용하는 데 제한된 권한을 가지고 있습니다. 권한이 있는 이니셔티브에만 예외가 생성됩니다.*

### <a name="are-there-any-recommendations-that-dont-support-exemption"></a>예외를 지원하지 않는 권장 사항이 있나요?

이러한 권장 사항은 예외를 지원하지 않습니다.

- 컨테이너 CPU 및 메모리 한도를 적용해야 함
- 권한 있는 컨테이너를 피해야 함
- 신뢰할 수 있는 레지스트리의 컨테이너 이미지만 배포해야 함
- 컨테이너는 허용되는 포트에서만 수신 대기해야 함
- 서비스는 허용되는 포트에서만 수신 대기해야 함
- 최소 권한 Linux 기능을 컨테이너에 적용해야 합니다.
- 변경 불가능한(읽기 전용) 루트 파일 시스템을 컨테이너에 적용해야 함
- 권한 상승을 포함하는 컨테이너를 사용하지 않아야 함
- 컨테이너를 루트 사용자로 실행하지 않아야 함
- 호스트 네트워킹 및 포트 사용을 제한해야 함
- 중요한 호스트 네임스페이스를 공유하는 컨테이너를 사용하지 않아야 함
- 손상된 컨테이너에서 노드 액세스를 제한하려면 Pod HostPath 볼륨 탑재 사용을 알려진 목록으로 제한해야 함
- 컨테이너 AppArmor 프로필의 재정의 또는 비활성화를 제한해야 함


## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 점수에 영향을 주지 않도록 권장 사항에서 리소스를 제외하는 방법을 알아보았습니다. 보안 점수에 대한 자세한 내용은 다음을 참조하세요.

- [Microsoft Defender for Cloud의 보안 점수](secure-score-security-controls.md)